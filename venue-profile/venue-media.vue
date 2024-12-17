<script setup lang="ts">
import { ref } from "vue";
import FileUpload from "primevue/fileupload";
import Galleria from "primevue/galleria";
import Button from "primevue/button";

const activeImages = ref<{ [key: string]: string | null }>({
  venueLogo: null,
  exteriorThumbnail: null,
  interiorThumbnail: null,
  bathroomThumbnail: null,
});

const waitingImages = ref<string[]>([]); // Holds images waiting to be assigned
const activeIndex = ref<number>(0); // Active index for Galleria
const uploadError = ref<string | null>(null); // Error message state

const uploadOrder = ["venueLogo", "exteriorThumbnail", "interiorThumbnail", "bathroomThumbnail"];

const selectedSlot = ref(uploadOrder[0]); // Default to first slot

const isValidImage = (file: File): boolean => {
  const allowedTypes = ["image/jpeg", "image/png", "image/gif"];
  return allowedTypes.includes(file.type);
};

const onUpload = (event: { files: File[] }) => {
  event.files.forEach((file) => {
    if (!isValidImage(file)) {
      uploadError.value = `Invalid file type: ${file.type}`;
      return;
    }
    if (file.size > 1000000) {
      uploadError.value = "File size exceeds 1MB limit.";
      return;
    }

    const objectURL = URL.createObjectURL(file);
    waitingImages.value.push(objectURL);
    uploadError.value = null; // Clear error if upload is successful
  });
};

const assignImageToSlot = (image: string) => {
  const slot = selectedSlot.value;
  if (activeImages.value[slot] === null) {
    activeImages.value[slot] = image;
    waitingImages.value = waitingImages.value.filter((img) => img !== image);
  } else {
    alert(`Slot ${slot} is already occupied!`);
  }
};

const removeImage = (key: string) => {
  const imageUrl = activeImages.value[key];
  if (imageUrl) {
    URL.revokeObjectURL(imageUrl); // Free memory
    activeImages.value[key] = null;
  }
};

const removeWaitingImage = (image: string) => {
  if (waitingImages.value.includes(image)) {
    URL.revokeObjectURL(image); // Free memory
    waitingImages.value = waitingImages.value.filter((img) => img !== image);
  }
};
</script>

<template>
  <div class="venue-media">
    <h3>Media</h3>
    <div class="image-upload-container">
      <div class="file-upload-section">
        <FileUpload
          mode="advanced"
          name="demo[]"
          custom-upload
          :multiple="true"
          accept="image/*"
          :max-file-size="1000000"
          @uploader="onUpload"
        />
        <p>Drag & Drop or click "Upload from Computer" to upload images</p>
        <p v-if="uploadError" class="error-message">{{ uploadError }}</p>
      </div>

      <div class="waiting-section">
        <h4>Waiting Area</h4>
        <div class="waiting-images">
          <div v-for="image in waitingImages" :key="image" class="waiting-thumbnail">
            <img :src="image" alt="Waiting Image" />
            <select v-model="selectedSlot" class="slot-select">
              <option v-for="key in uploadOrder" :value="key" :key="key">
                {{ key.split(/(?=[A-Z])/).join(" ") }}
              </option>
            </select>
            <br />
            <br />
            <Button @click="assignImageToSlot(image)" label="Assign to Slot" class="p-button-success" />
            <Button @click="removeWaitingImage(image)" label="Remove" class="p-button-danger" />
          </div>
        </div>
      </div>

      <div class="preview-section">
        <div v-for="(key, index) in Object.keys(activeImages)" :key="key" class="thumbnail-section">
          <p>{{ key.split(/(?=[A-Z])/).join(" ") }}</p>
          <div class="thumbnail-preview">
            <img v-if="activeImages[key]" :src="activeImages[key]" :alt="key" />
            <i v-else class="pi pi-image" />
          </div>
          <Button v-if="activeImages[key]" @click="removeImage(key)" label="Delete" class="p-button-danger" />
        </div>
      </div>

      <Galleria
        :value="Object.values(activeImages).filter((image) => image !== null)"
        :activeIndex="activeIndex"
        :responsive="[
          { breakpoint: '1024px', numVisible: 3 },
          { breakpoint: '600px', numVisible: 2 },
          { breakpoint: '480px', numVisible: 1 },
        ]"
        style="max-width: 800px; margin: auto; border-radius: 8px"
        showThumbnails="false"
        showItemNavigators="false"
      />
    </div>
  </div>
</template>

<style scoped>
.image-upload-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  max-width: 800px;
  margin: 0 auto;
}

.file-upload-section,
.waiting-section,
.preview-section {
  border: 1px solid #ddd;
  padding: 1rem;
  border-radius: 8px;
  margin-bottom: 1.5rem;
}

.waiting-images {
  display: flex;
  flex-wrap: wrap; /* Enables row layout with wrapping */
  gap: 1rem;
}

.waiting-thumbnail {
  width: calc(33.33% - 2rem); /* Each thumbnail takes up 33.33% of the container with spacing */
  text-align: center;
}

.waiting-thumbnail img {
  width: 100%;
  height: auto; /* Maintain aspect ratio */
  object-fit: contain;
  border-radius: 8px;
}

.thumbnail-preview {
  width: calc(33.33% - 2rem); /* Each thumbnail preview takes 33.33% of the container with spacing */
  text-align: center;
}

.thumbnail-preview img {
  width: 100%;
  height: auto; /* Maintain aspect ratio */
  object-fit: contain;
  border-radius: 8px;
}

.galleria-thumbnail img {
  width: 100%;
  height: auto; /* Maintain aspect ratio */
  object-fit: contain;
}

.slot-select {
  width: 100%;
  padding: 0.5rem;
  border-radius: 4px;
  border: 1px solid #ddd;
  margin-top: 0.5rem;
}

.assign-button,
.delete-button {
  display: block;
  width: 100%;
  margin-top: 0.5rem;
  margin-bottom: 0.25rem; /* Added small margin between buttons */
}

.assign-button {
  background-color: var(--primary-color);
  color: white;
  border: none;
  padding: 0.75rem;
  border-radius: 4px;
  font-size: 0.9rem;
}

.delete-button {
  background-color: var(--danger-color);
  color: white;
  border: none;
  padding: 0.75rem;
  border-radius: 4px;
  font-size: 0.9rem;
}

.error-message {
  color: red;
  font-weight: bold;
  font-size: 0.9rem;
  margin-top: 0.5rem;
}

h3,
h4 {
  margin-bottom: 1rem;
  font-weight: bold;
}

h4 {
  font-size: 1.2rem;
}

p {
  margin-top: 0.5rem;
  font-size: 1rem;
  color: #333;
}

.galleria {
  margin: auto;
  border-radius: 8px;
  max-width: 800px;
}
</style>
