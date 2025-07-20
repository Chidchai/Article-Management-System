<script setup lang="ts">
import { ref, watch, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import { debounce } from "lodash-es";

import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Button } from "@/components/ui/button";
import { Label } from "@/components/ui/label";
import { Select, SelectTrigger, SelectValue, SelectContent, SelectItem } from "@/components/ui/select";

import { TagsInput, TagsInputItem, TagsInputItemText, TagsInputItemDelete, TagsInputInput } from "@/components/ui/tags-input";
import RichTextEditor from "@/components/Article/RichTextEditor.vue";

const router = useRouter();

const form = ref({
  title: "",
  content: "",
  categories: [],
  tags: [],
  coverImage: "",
  status: "draft",
  metaTitle: "",
  metaDescription: "",
});

const categoryOptions = ref(["สินเชื่อบ้าน", "การเงินส่วนบุคคล", "ลงทุน"]);
const tagOptions = ref(["อัตราดอกเบี้ย", "รีไฟแนนซ์", "วางแผนการเงิน"]);

const saveDraft = debounce(() => {
  localStorage.setItem("article-draft", JSON.stringify(form.value));
}, 2000);

watch(form, () => saveDraft(), { deep: true });

onMounted(() => {
  const saved = localStorage.getItem("article-draft");
  if (saved) form.value = JSON.parse(saved);
});

const onCoverUpload = async (e: Event) => {
  const file = (e.target as HTMLInputElement).files?.[0];
  if (!file) return;

  const reader = new FileReader();
  reader.onload = () => {
    form.value.coverImage = reader.result as string;
  };
  reader.readAsDataURL(file);
};

const onSubmit = async () => {
  console.log("Form data:", form.value);
  localStorage.removeItem("article-draft");
  router.push("/admin/dashboard");
};
</script>

<template>
  <form class="space-y-6" @submit.prevent="onSubmit">
    <!-- Title -->
    <div class="space-y-2">
      <Label for="title">หัวข้อบทความ</Label>
      <Input id="title" v-model="form.title" placeholder="หัวข้อบทความ" required />
    </div>

    <!-- Tags -->
    <div class="space-y-2">
      <Label for="tags">แท็กที่เกี่ยวข้อง</Label>
      <TagsInput id="tags" v-model="tagOptions" class="w-full">
        <TagsInputItem v-for="tag in tagOptions" :key="tag" :value="tag">
          <TagsInputItemText />
          <TagsInputItemDelete />
        </TagsInputItem>

        <TagsInputInput placeholder="แท็กที่เกี่ยวข้อง" />
      </TagsInput>
    </div>

    <!-- Category -->
    <div class="space-y-2">
      <Label for="categories">หมวดหมู่</Label>
      <TagsInput id="categories" v-model="categoryOptions" class="w-full">
        <TagsInputItem v-for="category in categoryOptions" :key="category" :value="category">
          <TagsInputItemText />
          <TagsInputItemDelete />
        </TagsInputItem>

        <TagsInputInput placeholder="เลือกหมวดหมู่" />
      </TagsInput>
    </div>

    <!-- Cover Image Upload -->
    <div class="space-y-2">
      <Label for="cover">รูปหน้าปก</Label>
      <Input id="cover" type="file" accept="image/*" @change="onCoverUpload" />
      <img v-if="form.coverImage" :src="form.coverImage" alt="Cover" class="mt-2 w-48 rounded-md border" />
    </div>

    <!-- Content Editor -->
    <div class="space-y-2">
      <Label for="content">เนื้อหา</Label>
      <RichTextEditor id="content" v-model="form.content" />
    </div>

    <!-- Status -->
    <div class="space-y-2">
      <Label for="status">สถานะ</Label>
      <Select v-model="form.status">
        <SelectTrigger class="w-full">
          <SelectValue placeholder="เลือกสถานะ" />
        </SelectTrigger>
        <SelectContent>
          <SelectItem value="draft">ฉบับร่าง</SelectItem>
          <SelectItem value="published">เผยแพร่</SelectItem>
        </SelectContent>
      </Select>
    </div>

    <!-- SEO -->
    <div class="space-y-4">
      <div class="space-y-2">
        <Label for="metaTitle">Meta Title</Label>
        <Input id="metaTitle" v-model="form.metaTitle" placeholder="Meta Title" />
      </div>
      <div class="space-y-2">
        <Label for="metaDescription">Meta Description</Label>
        <Textarea id="metaDescription" v-model="form.metaDescription" placeholder="Meta Description" rows="3" />
      </div>
    </div>

    <!-- Save Button -->
    <Button type="submit">💾 บันทึกบทความ</Button>
  </form>
</template>
