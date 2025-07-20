<template>
  <section class="max-w-6xl mx-auto py-10 px-4 space-y-6">
    <div class="flex justify-between items-center">
      <h1 class="text-2xl font-bold">บทความทั้งหมด</h1>
      <NuxtLink to="/admin/articles/create">
        <Button>➕ เพิ่มบทความ</Button>
      </NuxtLink>
    </div>

    <Table v-if="articles.length">
      <thead>
        <tr>
          <th>หัวข้อ</th>
          <th>หมวดหมู่</th>
          <th>สถานะ</th>
          <th>อัปเดตล่าสุด</th>
          <th>จัดการ</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="article in articles" :key="article.id">
          <td>{{ article.title }}</td>
          <td>{{ article.categories.join(", ") }}</td>
          <td>
            <Badge :variant="article.status === 'published' ? 'success' : 'outline'">
              {{ article.status }}
            </Badge>
          </td>
          <td>{{ formatDate(article.updated_at) }}</td>
          <td>
            <NuxtLink :to="`/admin/articles/${article.id}`">
              <Button size="sm" variant="outline">แก้ไข</Button>
            </NuxtLink>
          </td>
        </tr>
      </tbody>
    </Table>

    <p v-else class="text-center text-muted-foreground">ไม่มีบทความ</p>
  </section>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { Button } from "@/components/ui/button";
import { Table } from "@/components/ui/table";
import { Badge } from "@/components/ui/badge";

const articles = ref<any[]>([]);

onMounted(async () => {
  const res = await $fetch("/api/articles");
  articles.value = res;
});

const formatDate = (d: string) =>
  new Date(d).toLocaleDateString("th-TH", {
    year: "numeric",
    month: "short",
    day: "numeric",
    hour: "2-digit",
    minute: "2-digit",
  });
</script>
