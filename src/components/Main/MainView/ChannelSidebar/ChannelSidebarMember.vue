<template>
  <sidebar-content-container title="メンバー">
    <empty-state v-if="isForceNotification">強制通知チャンネル</empty-state>
    <channel-sidebar-member-icons
      v-else-if="userIds.length > 0"
      :viewer-states="viewStates"
    />
    <empty-state v-else>メンバーはいません</empty-state>
  </sidebar-content-container>
</template>

<script lang="ts">
import { defineComponent, computed, PropType } from '@vue/composition-api'
import store from '@/store'
import { ChannelId, UserId } from '@/types/entity-ids'
import EmptyState from '@/components/UI/EmptyState.vue'
import SidebarContentContainer from '@/components/Main/MainView/MainViewSidebar/SidebarContentContainer.vue'
import ChannelSidebarMemberIcons from './ChannelSidebarMemberIcons.vue'
import { UserAccountState } from '@traptitech/traq'

export default defineComponent({
  name: 'ChannelSidebarMember',
  components: {
    EmptyState,
    ChannelSidebarMemberIcons,
    SidebarContentContainer
  },
  props: {
    channelId: { type: String as PropType<ChannelId>, required: true },
    viewerIds: { type: Array as PropType<UserId[]>, default: [] }
  },
  setup(props) {
    const isForceNotification = computed(
      () => store.state.entities.channels[props.channelId]?.force
    )
    const userIds = computed(() => store.state.domain.messagesView.subscribers)
    const viewStates = computed(() =>
      userIds.value
        .map(id => ({
          user: store.state.entities.users[id],
          active: props.viewerIds.includes(id)
        }))
        .filter(state => state.user?.state === UserAccountState.active)
        .sort((a, b) => {
          if (a.active === b.active) {
            return 0
          }
          return a.active ? -1 : 1
        })
    )
    return { userIds, isForceNotification, viewStates }
  }
})
</script>

<style lang="scss" module></style>
