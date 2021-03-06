<template>
  <div :class="$style.tags">
    <template v-if="detail === undefined">Now loading...</template>
    <template v-else>
      <ul :class="$style.list">
        <li v-for="tag in tags" :key="tag.tagId" :class="$style.tag">
          <div @click="onTagClick(tag.tagId)" :class="$style.content">
            <icon name="tag" mdi :class="$style.icon" :size="20" />
            <div :class="$style.text">
              {{ tag.tag }}
            </div>
          </div>
          <tags-tab-edit
            :tag-id="tag.tagId"
            :user-id="userId"
            :class="$style.edit"
          />
        </li>
      </ul>
      <tags-tab-add :user-id="userId" />
    </template>
  </div>
</template>

<script lang="ts">
import { defineComponent, computed, PropType } from '@vue/composition-api'
import store from '@/store'
import { UserDetail } from '@traptitech/traq'
import { TagId } from '@/types/entity-ids'
import Icon from '@/components/UI/Icon.vue'
import TagsTabAdd from '@/components/Main/Modal/UserModal/TagsTabAdd.vue'
import TagsTabEdit from '@/components/Main/Modal/UserModal/TagsTabEdit.vue'

export default defineComponent({
  name: 'TagsTab',
  props: {
    detail: Object as PropType<UserDetail>
  },
  setup(props) {
    const userId = computed(() => props.detail?.id)
    const tags = computed(() => props.detail?.tags ?? [])

    const onTagClick = (id: TagId) => {
      store.dispatch.ui.modal.pushModal({
        type: 'tag',
        id
      })
    }

    return {
      userId,
      tags,
      onTagClick
    }
  },
  components: {
    Icon,
    TagsTabAdd,
    TagsTabEdit
  }
})
</script>

<style lang="scss" module>
.tags {
  @include color-ui-primary;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.list {
  overflow-y: scroll;
  flex: 1 1;
}

.tag {
  margin: 16px 8px;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  &:first-child {
    // ナビゲーションと頭を揃える
    margin-top: 8px;
  }
}

.content {
  display: flex;
  align-items: center;
  min-width: 0;
}

.icon {
  margin-right: 8px;
  flex-shrink: 0;
}

.text {
  overflow-wrap: break-word;
  min-width: 0;
}
.edit {
  margin-left: 8px;
  width: 44px;
  flex-shrink: 0;
}
</style>
