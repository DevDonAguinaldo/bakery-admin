<script setup lang="ts">
import { ref } from "vue";

// Mock data for the dashboard stats
const stats = [
  {
    title: "Total Revenue",
    value: "$4,231.50",
    trend: "+12.5%",
    trendUp: true,
    icon: "i-lucide-dollar-sign",
    color: "text-emerald-500",
    bgColor: "bg-emerald-500/10",
  },
  {
    title: "New Orders",
    value: "45",
    trend: "+5.2%",
    trendUp: true,
    icon: "i-lucide-shopping-cart",
    color: "text-blue-500",
    bgColor: "bg-blue-500/10",
  },
  {
    title: "Active Products",
    value: "24",
    trend: "0%",
    trendUp: true,
    icon: "i-lucide-package",
    color: "text-violet-500",
    bgColor: "bg-violet-500/10",
  },
  {
    title: "Customers",
    value: "1,204",
    trend: "-2.1%",
    trendUp: false,
    icon: "i-lucide-users",
    color: "text-orange-500",
    bgColor: "bg-orange-500/10",
  },
];

// Table column definitions (TanStack Table format for Nuxt UI v4)
const columns = [
  { accessorKey: "id", header: "Order ID" },
  { accessorKey: "customer", header: "Customer" },
  { accessorKey: "status", header: "Status" },
  { accessorKey: "total", header: "Amount" },
  { accessorKey: "date", header: "Date" },
  { id: "actions", header: "" },
];

// Mock data for the table
const recentOrders = ref([
  {
    id: "ORD-001",
    customer: {
      name: "Alice Smith",
      email: "alice@example.com",
      avatar: "https://i.pravatar.cc/150?u=a",
    },
    status: "Delivered",
    total: 45.0,
    date: "2023-10-25",
  },
  {
    id: "ORD-002",
    customer: {
      name: "Bob Jones",
      email: "bob@example.com",
      avatar: "https://i.pravatar.cc/150?u=b",
    },
    status: "Processing",
    total: 12.5,
    date: "2023-10-25",
  },
  {
    id: "ORD-003",
    customer: {
      name: "Charlie Brown",
      email: "charlie@example.com",
      avatar: "https://i.pravatar.cc/150?u=c",
    },
    status: "Pending",
    total: 24.0,
    date: "2023-10-24",
  },
  {
    id: "ORD-004",
    customer: {
      name: "Diana Prince",
      email: "diana@example.com",
      avatar: "https://i.pravatar.cc/150?u=d",
    },
    status: "Delivered",
    total: 8.5,
    date: "2023-10-24",
  },
  {
    id: "ORD-005",
    customer: {
      name: "Evan Wright",
      email: "evan@example.com",
      avatar: "https://i.pravatar.cc/150?u=e",
    },
    status: "Cancelled",
    total: 15.0,
    date: "2023-10-23",
  },
]);

// Helper function to map status to badge color
const getStatusColor = (status: string) => {
  switch (status) {
    case "Delivered":
      return "success";
    case "Processing":
      return "warning";
    case "Pending":
      return "neutral";
    case "Cancelled":
      return "error";
    default:
      return "neutral";
  }
};

const formatCurrency = (value: number) => {
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency: "USD",
  }).format(value);
};

const lowStockItems = [
  { name: "Chocolate Croissant", stock: 12, threshold: 15 },
  { name: "Blueberry Muffin", stock: 0, threshold: 20 },
  { name: "Almond Biscotti", stock: 5, threshold: 10 },
];
</script>

<template>
  <div class="space-y-8">
    <!-- Page Header & Actions -->
    <div
      class="flex flex-col sm:flex-row sm:items-center justify-between gap-4"
    >
      <div>
        <h1
          class="text-2xl sm:text-3xl font-bold tracking-tight text-gray-900 dark:text-white"
        >
          Dashboard Overview
        </h1>
        <p class="text-gray-500 dark:text-gray-400 mt-1">
          Welcome back! Here's what's happening in your bakery today.
        </p>
      </div>
      <div class="flex items-center gap-3">
        <UButton
          color="neutral"
          variant="outline"
          icon="i-lucide-calendar"
          label="Today"
        />
        <UButton color="primary" icon="i-lucide-plus" label="New Order" />
      </div>
    </div>

    <!-- Stats Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 sm:gap-6">
      <UCard
        v-for="stat in stats"
        :key="stat.title"
        :ui="{ body: { padding: 'p-5 sm:p-6' } }"
        class="relative overflow-hidden group hover:ring-1 hover:ring-primary-500/50 transition-all duration-200"
      >
        <div class="flex items-center justify-between">
          <div class="space-y-1">
            <p class="text-sm font-medium text-gray-500 dark:text-gray-400">
              {{ stat.title }}
            </p>
            <p
              class="text-2xl sm:text-3xl font-semibold text-gray-900 dark:text-white"
            >
              {{ stat.value }}
            </p>
          </div>
          <div
            :class="[
              'p-3 rounded-xl flex items-center justify-center transition-transform group-hover:scale-110',
              stat.bgColor,
            ]"
          >
            <UIcon :name="stat.icon" :class="['w-6 h-6', stat.color]" />
          </div>
        </div>
        <div class="mt-4 flex items-center text-sm">
          <UBadge
            :color="stat.trendUp ? 'success' : 'error'"
            variant="subtle"
            size="sm"
            class="font-medium px-1.5 py-0.5 rounded-md flex items-center gap-1"
          >
            <UIcon
              :name="
                stat.trendUp ? 'i-lucide-trending-up' : 'i-lucide-trending-down'
              "
              class="w-3.5 h-3.5"
            />
            {{ stat.trend }}
          </UBadge>
          <span
            class="text-gray-500 dark:text-gray-400 ml-2 text-xs sm:text-sm"
          >
            vs last month
          </span>
        </div>
      </UCard>
    </div>

    <!-- Main Content Layout -->
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 lg:gap-8">
      <!-- Recent Orders Section -->
      <UCard class="lg:col-span-2" :ui="{ body: { padding: 'p-0 sm:p-0' } }">
        <template #header>
          <div
            class="flex flex-col sm:flex-row sm:items-center justify-between gap-4"
          >
            <div>
              <h2 class="text-lg font-semibold text-gray-900 dark:text-white">
                Recent Orders
              </h2>
              <p class="text-sm text-gray-500 dark:text-gray-400 mt-1">
                Latest transactions from your store
              </p>
            </div>
            <UButton
              label="View All"
              color="neutral"
              variant="ghost"
              trailing-icon="i-lucide-arrow-right"
              to="/orders"
            />
          </div>
        </template>

        <UTable :columns="columns" :data="recentOrders" class="w-full">
          <!-- Customer Cell -->
          <template #customer-cell="{ row }">
            <div class="flex items-center gap-3 py-1">
              <UAvatar
                :src="row.original.customer.avatar"
                :alt="row.original.customer.name"
                size="sm"
              />
              <div class="flex flex-col">
                <span class="font-medium text-gray-900 dark:text-white">{{
                  row.original.customer.name
                }}</span>
                <span class="text-xs text-gray-500 dark:text-gray-400">{{
                  row.original.customer.email
                }}</span>
              </div>
            </div>
          </template>

          <!-- Status Cell -->
          <template #status-cell="{ row }">
            <UBadge
              :color="getStatusColor(row.original.status)"
              variant="subtle"
              size="md"
              class="capitalize"
            >
              <div class="flex items-center gap-1.5">
                <span
                  class="w-1.5 h-1.5 rounded-full"
                  :class="{
                    'bg-green-500': row.original.status === 'Delivered',
                    'bg-yellow-500': row.original.status === 'Processing',
                    'bg-gray-500': row.original.status === 'Pending',
                    'bg-red-500': row.original.status === 'Cancelled',
                  }"
                ></span>
                {{ row.original.status }}
              </div>
            </UBadge>
          </template>

          <!-- Total Cell -->
          <template #total-cell="{ row }">
            <span class="font-medium text-gray-900 dark:text-white">
              {{ formatCurrency(row.original.total) }}
            </span>
          </template>

          <!-- Date Cell -->
          <template #date-cell="{ row }">
            <span class="text-sm text-gray-500 dark:text-gray-400">
              {{
                new Date(row.original.date).toLocaleDateString("en-US", {
                  month: "short",
                  day: "numeric",
                  year: "numeric",
                })
              }}
            </span>
          </template>

          <!-- Actions Cell -->
          <template #actions-cell>
            <div class="flex justify-end w-full">
              <UButton
                color="neutral"
                variant="ghost"
                icon="i-lucide-more-horizontal"
                aria-label="More actions"
              />
            </div>
          </template>
        </UTable>
      </UCard>

      <!-- Sidebar widgets -->
      <div class="space-y-6 lg:space-y-8">
        <!-- Low Stock Alerts -->
        <UCard>
          <template #header>
            <div class="flex items-center justify-between">
              <h2
                class="text-lg font-semibold text-gray-900 dark:text-white flex items-center gap-2"
              >
                <UIcon
                  name="i-lucide-alert-triangle"
                  class="w-5 h-5 text-amber-500"
                />
                Low Stock Alerts
              </h2>
            </div>
          </template>

          <div class="space-y-4">
            <div
              v-for="item in lowStockItems"
              :key="item.name"
              class="flex items-center justify-between pb-4 border-b border-gray-100 dark:border-gray-800 last:border-0 last:pb-0"
            >
              <div>
                <p class="font-medium text-gray-900 dark:text-white">
                  {{ item.name }}
                </p>
                <p class="text-xs text-gray-500 dark:text-gray-400 mt-0.5">
                  Threshold: {{ item.threshold }}
                </p>
              </div>
              <UBadge
                :color="item.stock === 0 ? 'error' : 'warning'"
                variant="subtle"
                size="sm"
              >
                {{ item.stock }} left
              </UBadge>
            </div>
          </div>

          <template #footer>
            <UButton
              label="Manage Inventory"
              color="neutral"
              variant="ghost"
              block
              to="/products"
            />
          </template>
        </UCard>

        <!-- Quick Actions -->
        <UCard>
          <template #header>
            <h2 class="text-lg font-semibold text-gray-900 dark:text-white">
              Quick Actions
            </h2>
          </template>

          <div class="grid grid-cols-2 gap-3">
            <UButton
              color="neutral"
              variant="outline"
              icon="i-lucide-plus"
              label="Product"
              class="justify-start shadow-sm"
              to="/products"
            />
            <UButton
              color="neutral"
              variant="outline"
              icon="i-lucide-user-plus"
              label="Customer"
              class="justify-start shadow-sm"
            />
            <UButton
              color="neutral"
              variant="outline"
              icon="i-lucide-tag"
              label="Discount"
              class="justify-start shadow-sm"
            />
            <UButton
              color="neutral"
              variant="outline"
              icon="i-lucide-file-text"
              label="Report"
              class="justify-start shadow-sm"
            />
          </div>
        </UCard>
      </div>
    </div>
  </div>
</template>
