<script setup>
import { useToast } from 'primevue/usetoast';
import { computed, ref, watch, nextTick } from 'vue';
import axios from 'axios';

const BASE_URL = 'http://127.0.0.1:8000';
const toast = useToast();
const imageUrl = ref('');
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
    label: '原图待识别'
  },
  {
    label: '分割结果图'
  },
  {
    label: '分割结果掩码'
  },
  {
    label: '井圈类别判定'
  },
  {
    label: '目标检测结果锚框'
  },
  {
    label: '最终结果图'
  }
]);

const taglist = ref([
  { id: 0, tags: [{ severity: 'danger', tag: '井盖缺失' }] },
  { id: 1, tags: [{ severity: 'danger', tag: '井盖未盖' }] },
  { id: 2, tags: [{ severity: 'danger', tag: '井盖破损' }] },
  { id: 3, tags: [{ severity: 'warning', tag: '井圈问题' }] },
  { id: 4, tags: [{ severity: 'success', tag: '井盖完好' }] }
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

const nextprogress = () => {
  active.value += 1;
  if (active.value === 6) {
    active.value = 0;
  }
  // if (selectedPic.value ? selectedPic.value.id : null === null) {
  //   active.value -= 1;
  //   toast.add({
  //     severity: 'error',
  //     summary: '失败',
  //     detail: `请选择图片`,
  //     life: 3000
  //   });
  //   return;
  // }
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
  const randomDelaySeconds = 0.4 + Math.random() * 0.1;
  setTimeout(() => {
    pic_delay_url.value = newValue;
  }, randomDelaySeconds * 1000);
});
watch(selectedPic, () => {
  if (selectedPic.value === null) {
    pic_delay_url.value = 'layout/images/jinggai_dark.svg';
    return;
  }

  if (active.value === 0) {
    active.value = 6;
    nextTick(() => {
      active.value = 0;
    });
  } else {
    active.value = 0;
  }
});
</script>

<template>
  <div class="grid">
    <div class="col-12">
      <Steps v-model:activeStep="active" :model="items" />
    </div>
    <div class="col-12">
      <div class="card middle-card">
        <div v-if="active === 5" class="card">
          最终结果：
          <Tag v-for="t in tagtag" :key="t.tag" :severity="t.severity">{{ t.tag }}</Tag>
        </div>
        <Image :src="pic_delay_url" alt="Image" width="500" preview />
        <br>
        <div class="card flex justify-content-center gap-2">
          <ButtonGroup>
            <Button label="开始识别" icon="pi pi-check" @click="nextprogress" />
            <Button label="取消" icon="pi pi-times" @click="resetprogress" />
          </ButtonGroup>
          <Dropdown v-model="selectedPic" :options="Pic" optionLabel="name" placeholder="选择图片" checkmark
            :highlightOnSelect="false" class="w-full md:w-14rem" />
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
.middle-card {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>
