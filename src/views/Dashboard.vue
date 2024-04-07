<script setup>
import { useToast } from 'primevue/usetoast';
import { computed, ref } from 'vue';
import axios from 'axios';

const BASE_URL = 'http://127.0.0.1:8000';
const toast = useToast();
const imageUrl = ref('');
const images = ref([]);
const show_loading = ref(false);
const has_image = computed(() => {
  return images.value.length > 0;
});

const onUpload = async (eventData) => {
  const files = eventData.files;
  show_loading.value = true;
  if (!files.length) return;

  const formData = new FormData();
  for (let file of files) {
    formData.append('files', file); // 使用'files'而不是'file'
  }

  try {
    const response = await axios.post(`${BASE_URL}/api/detects`, formData);
    if (response.data.data && response.data.data.length > 0) {
      images.value = response.data.data;
      show_loading.value = false;
    }
    toast.add({
      severity: 'success',
      summary: '上传成功',
      detail: '文件已成功上传',
      life: 3000
    });
  } catch (error) {
    console.error('Upload error:', error);
    toast.add({
      severity: 'error',
      summary: '上传失败',
      detail: error.response ? error.response.data.detail : '上传失败，请稍后再试。',
      life: 3000
    });
    show_loading.value = false;
  }
};

const logoUrl = computed(() => {
  return `layout/images/jinggai_dark.svg`;
});
</script>

<template>
  <div class="grid">
    <div class="col-12">
      <div class="card">
        <div v-if="has_image">
          <Galleria :value="images" :responsiveOptions="galleriaResponsiveOptions" :numVisible="7" :circular="true" containerStyle="max-width: 800px; margin: auto">
            <template #item="slotProps">
              <img :src="slotProps.item.itemImageSrc" :alt="slotProps.item.alt" style="width: 100%; display: block" />
            </template>
            <template #thumbnail="slotProps">
              <img :src="slotProps.item.thumbnailImageSrc" :alt="slotProps.item.alt" style="width: 80px; height: 60px; display: block; object-fit: contain" />
            </template>
          </Galleria>
        </div>
        <div v-else>
          <div class="flex justify-content-center align-items-center p-3">
            <Image :src="imageUrl ? imageUrl : logoUrl" alt="Image" preview height="500px" class="img-preview" />
          </div>
        </div>
      </div>
    </div>
    <div class="col-12">
      <div class="card">
        <FileUpload name="demo[]" @uploader="onUpload" :multiple="true" accept="image/*" :maxFileSize="1000000" customUpload />
      </div>
    </div>
    <Toast />
    <Dialog v-model:visible="show_loading" modal header="Loading...." :style="{ width: '50rem' }" :breakpoints="{ '1199px': '75vw', '575px': '90vw' }">
      <ProgressBar mode="indeterminate" style="height: 6px"></ProgressBar>
    </Dialog>
  </div>
</template>

<style lang="scss" scoped>
.middle-card {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 60vh;
}
</style>
