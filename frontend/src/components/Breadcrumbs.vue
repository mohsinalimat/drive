<template>
  <div class="flex justify-items-center items-center text-base">
    <div class="flex text-md line-clamp-1 sm:text-lg font-medium">
      <template
        v-for="(item, index) in $store.state.breadcrumbs"
        :key="item.label"
      >
        <template v-if="index !== $store.state.breadcrumbs.length - 1">
          <router-link
            v-if="item.route"
            class="text-gray-600 hover:text-gray-800"
            :to="item.route"
          >
            <span> {{ item.label }}</span>
            <span class="mx-1">/</span>
          </router-link>
          <span v-else class="text-gray-600"
            ><span> {{ item.label }}</span> <span class="mx-1">/</span></span
          >
        </template>
        <span v-else class="text-black">
          {{ item.label }}
        </span>
      </template>
    </div>
  </div>
</template>
