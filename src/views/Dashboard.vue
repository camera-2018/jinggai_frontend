<script setup>
import { useToast } from 'primevue/usetoast';
import { computed, ref } from 'vue';
import axios from 'axios';

const toast = useToast();
const imageUrl = ref('');
const onUpload = async (eventData) => {
  const files = eventData.files;
  if (!files.length) return;

  const formData = new FormData();
  for (let file of files) {
    formData.append('file', file);
  }

  try {
    const response = await axios.post('http://127.0.0.1:8000/api/detect', formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    });
    imageUrl.value = response.data.url;
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
      detail: error.response ? error.response.data : '上传失败，请稍后再试。',
      life: 3000
    });
  }
};

const logoUrl = computed(() => {
  return `layout/images/jinggai_dark.svg`;
});
</script>

<template>
  <div class="grid">
    <div class="col-12">
      <div class="card middle-card">
        <Image :src="imageUrl ? imageUrl : logoUrl" alt="Image" width="250" preview class="img-preview" />
      </div>
    </div>
    <div class="col-12">
      <div class="card">
        <FileUpload name="demo[]" @uploader="onUpload" :multiple="true" accept="image/*" :maxFileSize="1000000" customUpload />
      </div>
    </div>
    <Toast />
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
