<script setup lang="ts">
import { ref, computed } from "vue";
import { useSupabaseClient } from "#imports";

const supabase = useSupabaseClient();

// Fetch products from Supabase
const {
  data: products,
  pending,
  refresh,
} = await useAsyncData("products", async () => {
  const { data, error } = await supabase
    .from("products")
    .select("*")
    .order("created_at", { ascending: false });

  if (error) {
    console.error("Error fetching products:", error);
    return []; // Return empty array on error so empty state triggers
  }

  return data || [];
});

const searchQuery = ref("");

// Compute filtered products based on search
const filteredProducts = computed(() => {
  if (!products.value) return [];
  if (!searchQuery.value) return products.value;

  const query = searchQuery.value.toLowerCase();
  return products.value.filter(
    (p: any) =>
      p.name?.toLowerCase().includes(query) ||
      p.category?.toLowerCase().includes(query),
  );
});

// Define table columns using TanStack Table format for Nuxt UI v4
const columns = [
  { accessorKey: "name", header: "Product Name" },
  { accessorKey: "category", header: "Category" },
  { accessorKey: "price", header: "Price" },
  { accessorKey: "stock", header: "Stock" },
  { accessorKey: "status", header: "Status" },
  { id: "actions", header: "" },
];

// Format price as currency
const formatCurrency = (value: number) => {
  if (value == null) return "-";
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency: "USD",
  }).format(value);
};

// Get badge color based on stock status
const getStatusColor = (status: string) => {
  if (!status) return "neutral";
  switch (status.toLowerCase()) {
    case "active":
      return "success";
    case "low stock":
      return "warning";
    case "out of stock":
      return "error";
    default:
      return "neutral";
  }
};
</script>

<template>
  <div class="space-y-6">
    <!-- Page Header -->
    <div
      class="flex flex-col sm:flex-row sm:items-center justify-between gap-4"
    >
      <div>
        <h1
          class="text-2xl font-bold tracking-tight text-gray-900 dark:text-white"
        >
          Products
        </h1>
        <p class="text-gray-500 dark:text-gray-400 mt-1">
          Manage your bakery inventory, pricing, and stock levels.
        </p>
      </div>
      <div class="flex items-center gap-2">
        <UButton
          color="neutral"
          variant="outline"
          icon="i-lucide-refresh-cw"
          :loading="pending"
          @click="refresh"
        />
        <UButton color="primary" icon="i-lucide-plus" label="Add Product" />
      </div>
    </div>

    <!-- Products Card Container -->
    <UCard :ui="{ body: { padding: 'p-0 sm:p-0' } }">
      <!-- Toolbar -->
      <div
        class="p-4 border-b border-gray-200 dark:border-gray-800 flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4"
      >
        <UInput
          v-model="searchQuery"
          icon="i-lucide-search"
          placeholder="Search products..."
          class="w-full sm:max-w-sm"
          :disabled="pending && !products?.length"
        />
        <UButton
          color="neutral"
          variant="ghost"
          icon="i-lucide-filter"
          label="Filter"
          :disabled="pending && !products?.length"
        />
      </div>

      <!-- Loading Skeletons State -->
      <div v-if="pending" class="p-4 space-y-4">
        <!-- Skeleton Header Row -->
        <div
          class="flex items-center gap-4 pb-4 border-b border-gray-100 dark:border-gray-800"
        >
          <USkeleton class="h-6 w-1/4" />
          <USkeleton class="h-6 w-1/4" />
          <USkeleton class="h-6 w-1/4" />
          <USkeleton class="h-6 w-1/4" />
        </div>
        <!-- Skeleton Body Rows -->
        <div v-for="i in 5" :key="i" class="flex items-center gap-4 py-2">
          <USkeleton class="h-10 w-full" />
        </div>
      </div>

      <!-- Empty State -->
      <div
        v-else-if="filteredProducts.length === 0"
        class="flex flex-col items-center justify-center py-16 px-4 text-center"
      >
        <div
          class="p-4 bg-gray-50 dark:bg-gray-800/50 rounded-full mb-4 border border-gray-100 dark:border-gray-800"
        >
          <UIcon
            name="i-lucide-package-open"
            class="w-8 h-8 text-gray-400 dark:text-gray-500"
          />
        </div>

        <h3 class="text-lg font-semibold text-gray-900 dark:text-white">
          No products found
        </h3>
        <p class="text-gray-500 dark:text-gray-400 mt-1 max-w-sm">
          {{
            searchQuery
              ? "We couldn't find any products matching your search. Try adjusting your keywords."
              : "Get started by adding your first product to the bakery catalog."
          }}
        </p>

        <div class="mt-6 flex items-center gap-3">
          <UButton
            v-if="searchQuery"
            color="neutral"
            variant="outline"
            label="Clear search"
            @click="searchQuery = ''"
          />
          <UButton
            v-else
            icon="i-lucide-plus"
            color="primary"
            label="Add Product"
          />
        </div>
      </div>

      <!-- Data Table -->
      <UTable v-else :columns="columns" :data="filteredProducts" class="w-full">
        <!-- Price Cell Template -->
        <template #price-cell="{ row }">
          <span class="font-medium text-gray-900 dark:text-white">
            {{ formatCurrency(row.original.price) }}
          </span>
        </template>

        <!-- Status Cell Template -->
        <template #status-cell="{ row }">
          <UBadge
            :color="getStatusColor(row.original.status)"
            variant="subtle"
            size="sm"
          >
            {{ row.original.status || "Draft" }}
          </UBadge>
        </template>

        <!-- Actions Cell Template -->
        <template #actions-cell="{ row }">
          <div class="flex items-center justify-end gap-1">
            <UButton
              icon="i-lucide-edit-2"
              color="neutral"
              variant="ghost"
              size="sm"
              aria-label="Edit product"
            />
            <UButton
              icon="i-lucide-trash-2"
              color="error"
              variant="ghost"
              size="sm"
              aria-label="Delete product"
            />
          </div>
        </template>
      </UTable>
    </UCard>
  </div>
</template>
