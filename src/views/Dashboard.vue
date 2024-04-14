<script setup>
import { useToast } from 'primevue/usetoast';
import { computed, ref, watch } from 'vue';
import ImgZoom from '../components/ImageZoom.vue'
const toast = useToast();
const show_loading = ref(false);
const selectedPic = ref(null);
const pic_delay_url = ref('layout/images/jinggai_dark.svg');
const Pic = ref([
  { name: '图片1', id: 0 },
  { name: '图片2', id: 1 },
  { name: '图片3', id: 2 },
  { name: '图片4', id: 3 },
  { name: '图片5', id: 4 }
]);
const elapsedTime = ref('00:00:000');
let timer = null;

function startTimer() {
  const startTime = Date.now();
  timer = setInterval(() => {
    const diff = Date.now() - startTime;
    const seconds = Math.floor(diff / 1000);
    const milliseconds = diff % 1000;
    elapsedTime.value = `${seconds.toString().padStart(2, '0')}:${milliseconds.toString().padStart(3, '0')}`;
  }, 1);
}

function stopTimer() {
  clearInterval(timer);
  timer = null;
  elapsedTime.value = '00:00:000';
}

watch(show_loading, (newValue) => {
  if (newValue) {
    startTimer();
  } else {
    stopTimer();
  }
});
const items = ref([
  {
    label: '待识别'
  },
  {
    label: '最终结果'
  }
]);

const labels = ref([
  { label: '分割结果图' },
  { label: '分割结果掩码' },
  { label: '井圈类别判定' },
  { label: '目标检测结果锚框' },
  { label: '最终结果图' }
]);

const taglist = ref([
  { id: 0, tags: [{ severity: 'danger', tag: '井盖缺失' },{ severity: 'info', tag: '48%' }] },
  { id: 1, tags: [{ severity: 'danger', tag: '井盖未盖' },{ severity: 'info', tag: '95%' }] },
  { id: 2, tags: [{ severity: 'danger', tag: '井盖破损' },{ severity: 'info', tag: '46%' }] },
  { id: 3, tags: [{ severity: 'warning', tag: '井圈问题' },{ severity: 'info', tag: '49%' }] },
  { id: 4, tags: [{ severity: 'success', tag: '井盖完好' },{ severity: 'info', tag: '49%' }] }
])

const tagtag = computed(() => {
  let id = selectedPic.value ? selectedPic.value.id : null;
  if (id !== null) {
    return taglist.value[id].tags;
  }
  else return [];
})

const active = ref(0);
const picurl = computed(() => {
  let id = selectedPic.value ? selectedPic.value.id : null;
  if (id !== null) {
    return `jinggaipic/${id}/${active.value}.jpg`;
  }
  else return 'layout/images/jinggai_dark.svg';
});

const pic_show_all_url = computed(() => {
  let id = selectedPic.value ? selectedPic.value.id : null;
  if (id !== null) {
    return [`jinggaipic/${id}/1.jpg`, `jinggaipic/${id}/2.jpg`, `jinggaipic/${id}/3.jpg`, `jinggaipic/${id}/4.jpg`, `jinggaipic/${id}/5.jpg`]
  }
  else return 'layout/images/jinggai_dark.svg';
});

const nextprogress = () => {
  active.value += 1;
  if (active.value === 2) {
    active.value = 0;
  }
  if (pic_show_all_url.value === 'layout/images/jinggai_dark.svg') {
    active.value -= 1;
    toast.add({
      severity: 'info',
      summary: '请选择图片',
      life: 3000
    });
    return;
  }
  show_loading.value = true;

  const randomDelaySeconds = 0.3 + Math.random() * 0.1;

  setTimeout(() => {
    show_loading.value = false;
    toast.add({
      severity: 'success',
      summary: '识别成功',
      detail: `已成功识别，耗时 ${elapsedTime.value} s`,
      life: 3000
    });
  }, randomDelaySeconds * 1000);
};

const resetprogress = () => {
  active.value = 0;
};

watch(picurl, (newValue) => {
  pic_delay_url.value = newValue;
});
watch(selectedPic, () => {
  if (selectedPic.value === null) {
    pic_delay_url.value = 'layout/images/jinggai_dark.svg';
    return;
  }
  active.value = 0;
});
</script>

<template>
  <div class="grid">
    <div class="col-12">
      <Steps v-model:activeStep="active" :model="items" />
    </div>
    <div class="col-12">
      <div class="card middle-card">
        <div v-if="active === 1" class="card">
          最终结果：
          <Tag v-for="t in tagtag" :key="t.tag" :severity="t.severity" class="tags">{{ t.tag }}</Tag>
        </div>
        <div v-if="active === 1" class="is_5_pic">
          <div class="left-side">
            <div class="left-side-cld text" v-for="(pic, index) in pic_show_all_url.slice(0, 3)" :key="index">
              <ImgZoom :src="pic" alt="Image" />
              <div class="image-label">{{ labels[index].label }}</div>
            </div>
            <div class="left-side-cld text">
              <ImgZoom :src="pic_show_all_url[3]" alt="Image" :key="4" />
              <div class="image-label">{{ labels[3].label }}</div>
            </div>
          </div>
          <div class="right-side">
            <div class="right-side-cld text">
              <ImgZoom :src="pic_show_all_url[4]" alt="Image" :key="5" />
              <div class="image-label">{{ labels[4].label }}</div>
            </div>
          </div>
        </div>
        <ImgZoom v-else :src="pic_delay_url" alt="Image" width="500" preview />
        <br />
        <div class="card flex justify-content-center gap-2">
          <Dropdown v-model="selectedPic" :options="Pic" optionLabel="name" placeholder="选择图片" checkmark
          :highlightOnSelect="false" class="w-full md:w-14rem" />
          <ButtonGroup>
            <Button v-if="active === 0" label="开始识别" icon="pi pi-check" @click="nextprogress" />
            <Button v-if="active === 1" label="取消" icon="pi pi-times" @click="resetprogress" />
          </ButtonGroup>
        </div>
      </div>
    </div>
    <Toast />
    <Dialog v-model:visible="show_loading" modal :header="`正在识别井盖细节，请稍等... ${elapsedTime}`" :style="{ width: '50rem' }"
      :breakpoints="{ '1199px': '75vw', '575px': '90vw' }">
      <ProgressBar mode="indeterminate" style="height: 6px"></ProgressBar>
    </Dialog>
  </div>
</template>

<style lang="scss" scoped>
.tags{
  margin: 0 5px;
}
.middle-card {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.text {
  text-align: center;
  font-size: 1.5rem;
  font-weight: 700;
}
.is_5_pic {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto;
  gap: 10px;
  height: 100%;
}

.left-side {
  display: grid;
  grid-template-rows: 1fr auto;
}

.left-side-cld,
.right-side-cld {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.left-side .left-side-cld:nth-child(1),
.left-side .left-side-cld:nth-child(2),
.left-side .left-side-cld:nth-child(3) {
  grid-row: 1;
}

.left-side .left-side-cld:nth-child(4) {
  grid-row: 2;
  grid-column: 2 / 3;
  align-items: center;
}

.right-side {
  display: flex;
  align-items: center; /* 垂直居中 */
  justify-content: center; /* 水平居中 */
}

.is_5_pic img {
  width: 100%; /* 让图片填充整个网格单元 */
  height: auto; /* 保持图片的原始宽高比 */
  max-width: 500px; /* 最大宽度限制，可根据实际需求调整 */
}
</style>
