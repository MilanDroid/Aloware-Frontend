<template>
  <div
    class="comment w-100 d-flex flex-column justify-content-strech align-items-center border shadow mx-auto"
    :class="!parentId ? 'border-5' : 'border-3'"
  >
    <div class="content w-100 my-3">
      <div class="user d-flex align-items-center justify-content-between">
        <div class="mx-3">
          <div
            class="user-thumbnail d-flex justify-content-center align-items-center border border-3 rounded-circle text-brand-muted"
          >
            <b>{{ imageThumbnail }}</b>
          </div>
        </div>
        <div class="d-flex flex-column justify-content-end mx-3">
          <div class="user-nick-name">
            <small class="text-muted">@{{ userNick }}</small>
          </div>

          <div class="user-name">
            <b>{{ userName }}</b>
          </div>
        </div>
      </div>

      <div
        class="body d-flex flex-column justify-content-center align-items-end px-3 position-relative"
      >
        <small class="time text-brand-muted my-2">
          {{ createdAt }}
        </small>
        <span class="fw-regular text-end">{{ body }}</span>

        <div
          v-if="replyStatus"
          class="w-100"
          style="position: absolute; bottom: 65px; right: 0"
        >
          <AppReply :comment-id="commentId" @hide="replyStatus = false" />
        </div>

        <div
          class="options w-100 d-flex justify-content-end align-items-center mt-3 mb-4"
        >
          <div v-if="depth < 2" class="border border-3 rounded-circle p-2 mt-3 ms-3 toggle-active-deep user-select-none cursor-pointer" @click="toggleReply">
            <svg width="24" height="24" stroke-width="1.5" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M7 12L17 12" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M7 8L13 8" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M3 20.2895V5C3 3.89543 3.89543 3 5 3H19C20.1046 3 21 3.89543 21 5V15C21 16.1046 20.1046 17 19 17H7.96125C7.35368 17 6.77906 17.2762 6.39951 17.7506L4.06852 20.6643C3.71421 21.1072 3 20.8567 3 20.2895Z" stroke="currentColor" stroke-width="1.5"/>
            </svg>
          </div>
          <div class="border border-3 rounded-circle p-2 mt-3 ms-3 toggle-active-deep user-select-none cursor-pointer" @click="toggleLike">
            <svg width="24" height="24" stroke-width="1.5" viewBox="0 0 24 24" :fill="likeStatus ? 'red' : 'none'" xmlns="http://www.w3.org/2000/svg">
              <path d="M22 8.86222C22 10.4087 21.4062 11.8941 20.3458 12.9929C17.9049 15.523 15.5374 18.1613 13.0053 20.5997C12.4249 21.1505 11.5042 21.1304 10.9488 20.5547L3.65376 12.9929C1.44875 10.7072 1.44875 7.01723 3.65376 4.73157C5.88044 2.42345 9.50794 2.42345 11.7346 4.73157L11.9998 5.00642L12.2648 4.73173C13.3324 3.6245 14.7864 3 16.3053 3C17.8242 3 19.2781 3.62444 20.3458 4.73157C21.4063 5.83045 22 7.31577 22 8.86222Z" stroke="currentColor" stroke-linejoin="round"/>
            </svg>
          </div>
        </div>
      </div>
    </div>

    <div class="comments container-fluid">
      <div class="row">
        <AppComment
          v-for="(child, key) in childs"
          :key="key"
          :comment="child"
          :depth="childsDepth"
          class="col-12 my-3"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import moment from "moment";

import AppReply from "./AppReply.vue";

export default defineComponent({
  name: "AppComment",
  components: {
    AppComment: () => import("@/components/AppComment.vue"),
    AppReply,
  },
  props: {
    comment: Object,
    depth: {
      type: Number,
      default: 0,
    },
  },
  setup: (props) => {
    const commentData = props.comment ?? [];
    const commentId = ref<number>(commentData.id);
    const parentId = ref<number | null>(commentData.parent_id ?? null);
    const childs = ref<unknown>(commentData.childs ?? []);
    const childsDepth = ref<number>(props.depth + 1);
    const body = ref<string | null>(commentData.body ?? null);
    const userNick = ref<string | null>(
      commentData?.user?.email.split("@")[0] ?? null
    );
    const userName = ref<string | null>(commentData?.user?.name ?? null);
    const createdAt = ref<string | null>(
      moment(commentData.created_at).format("LL") ?? null
    );

    const getThumbnailLetters = () => {
      // eslint-disable-next-line prettier/prettier
      const words = userName.value?.split(' ') ?? [];
      return words.length >= 2
        ? words
            ?.slice(0, 2)
            .map((word) => word.charAt(0))
            .join("")
        : " ";
    };
    const imageThumbnail = ref<string>(getThumbnailLetters());

    const replyStatus = ref<boolean>(false);
    const toggleReply = () => (replyStatus.value = !replyStatus.value);

    const likeStatus = ref<boolean>(false);
    const toggleLike = () => (likeStatus.value = !likeStatus.value);

    return {
      commentId,
      parentId,
      childs,
      childsDepth,
      body,
      userNick,
      userName,
      imageThumbnail,
      createdAt,
      replyStatus,
      toggleReply,
      likeStatus,
      toggleLike,
    };
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$userSectionHeight: 80px;
$optionsSectionHeight: 30px;

.comment {
  background-color: #f7fcfd;
  min-height: $userSectionHeight;
  border-radius: 1.3rem;

  & .content {
    border: 0.25rem solid rgba(80, 80, 80, 0.75);
    border-radius: 1.3rem;
  }

  & .body {
    border-bottom-left-radius: 1.3rem;
    border-bottom-right-radius: 1.3rem;
    min-height: $userSectionHeight * 0.61382;
    margin-bottom: 0.25rem;
    margin-left: 0.25rem;
    margin-right: 0.25rem;
  }

  & .user {
    background-color: rgba(0, 0, 0, 0.06138);
    border-bottom: 0.25rem solid rgba(0, 0, 0, 0.15);
    border-top-left-radius: 1.6rem;
    border-top-right-radius: 1.6rem;
    height: $userSectionHeight;
  }

  & .user-thumbnail {
    height: $userSectionHeight * 0.7;
    width: $userSectionHeight * 0.7;
  }

  & .user-nick-name {
    font-size: 86%;
    text-align: end;
  }

  & .user-name {
    font-style: italic;
    text-align: end;
  }

  & .time {
    font-size: 80%;
    font-weight: 500;
    font-style: italic;
  }

  & .options {
    height: $optionsSectionHeight;
  }
}
</style>
