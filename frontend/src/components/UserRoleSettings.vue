<template>
  <div
    class="flex h-full w-full flex-col items-start justify-start rounded-lg text-center"
  >
    <div class="flex items-center w-full mb-1">
      <div class="flex flex-col items-start">
        <h1 class="font-semibold">User Groups</h1>
        <span class="text-sm py-1 mb-4 text-gray-600">
          Create and manage user groups
        </span>
      </div>
      <Button
        size="sm"
        variant="subtle"
        icon-left="plus"
        class="ml-auto"
        @click="CreateRoleDialog = !CreateRoleDialog"
      >
        New
      </Button>
    </div>

    <div class="flex items-center w-full mb-1"></div>

    <div
      v-if="AllRoles?.length"
      class="flex flex-col h-full w-full overflow-x-hidden overflow-y-scroll"
    >
      <div
        v-for="group in AllRoles"
        :key="group.name"
        class="flex flex-col content-center items-start w-full hover:bg-gray-50 rounded cursor-pointer group"
        @click="viewGroupDetails(group.name)"
      >
        <div class="flex items-center w-full py-4 px-1">
          <Avatar size="xl" :label="group.name" />
          <span class="ml-2 text-base text-gray-900">{{ group.name }}</span>
          <Button
            icon="trash-2"
            variant="minimal"
            class="z-40 text-red-500 ml-auto invisible group-hover:visible"
            @click.prevent.stop="
              $resources.deleteUserGroup.submit({
                group_name: group.name,
              })
            "
          >
            Delete
          </Button>
        </div>
        <div class="mx-3 h-px border-t border-gray-200 w-full"></div>
      </div>
    </div>
    <div v-else class="h-1/2 w-full flex flex-col items-center justify-center">
      <FeatherIcon class="h-10 stroke-1 text-gray-600" name="users" />
      <span class="text-gray-800 text-sm">You dont have any groups</span>
    </div>
    <!--     <span class="text-lg font-medium">Organization Settings</span>
    <div class="flex justify-between w-full pt-4 pb-8">
      <div class="flex flex-col items-start">
        <span class="text-base font-medium">Name</span>
        <span class="text-sm py-1 text-gray-600">
          All Drive users on this instance are a part of this
        </span>
      </div>
      <Button class="font-medium text-gray-800" appearance="minimal">
        <template #prefix>
          <FeatherIcon name="edit" class="text-gray-800 stroke-1.5 h-4" />
        </template>
        Frappe
      </Button>
    </div> -->
    <span class="text-sm pb-4 text-gray-600 mt-auto">
      This page is only available to Administrators
    </span>
  </div>
  <NewRoleDialog
    v-if="CreateRoleDialog"
    v-model="CreateRoleDialog"
    @success="
      () => {
        CreateRoleDialog = false
        $resources.getUserGroups.fetch()
      }
    "
  />
  <RoleDetailsDialog
    v-if="EditRoleDialog"
    v-model="EditRoleDialog"
    :role-name="RoleName"
    @success="
      () => {
        EditRoleDialog = false
        $resources.getUserGroups.fetch()
      }
    "
  />
</template>
<script>
import { Avatar, FeatherIcon } from "frappe-ui"
import RoleDetailsDialog from "@/components/RoleDetailsDialog.vue"
import NewRoleDialog from "./NewRoleDialog.vue"

export default {
  name: "UserRoleSettings",
  components: {
    Avatar,
    RoleDetailsDialog,
    NewRoleDialog,
    FeatherIcon,
  },
  data() {
    return {
      RoleName: "",
      UsersInRole: [],
      CreateRoleDialog: false,
      EditRoleDialog: false,
      AllRoles: null,
      errorMessage: "",
    }
  },
  computed: {
    memberEmails() {
      let x = []
      this.UsersInRole.forEach((user) => x.push(user.email))
      return x
    },
  },
  methods: {
    viewGroupDetails(value) {
      this.RoleName = value
      this.EditRoleDialog = !this.EditRoleDialog
    },
  },
  resources: {
    deleteUserGroup() {
      return {
        url: "drive.utils.user_group.delete_user_group",
        params: {
          group_name: null,
        },
        onSuccess() {
          this.errorMessage = ""
          this.$resources.getUserGroups.fetch()
        },
        onError(data) {
          console.log(data)
          this.errorMessage = data
        },
        auto: false,
      }
    },
    getUserGroups() {
      return {
        url: "drive.utils.user_group.get_name_of_all_user_groups",
        onSuccess(data) {
          this.AllRoles = data
        },
        onError(data) {
          console.log(data)
        },
        auto: true,
      }
    },
  },
}
</script>
