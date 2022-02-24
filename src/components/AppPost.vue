<template>
  <div>
    <div
      class="post w-100 d-flex flex-column justify-content-strech align-items-center shadow border border-5 mx-auto p-3"
    >
      <div class="content w-100">
        <div
          class="body d-flex flex-column justify-content-between align-items-center position-relative"
        >
          <div
            class="d-flex flex-fill justify-content-center aling-items-center"
          >
            <b class="align-self-center">Hello, this is my post! :)</b>
          </div>

          <div v-if="replyStatus" class="w-75" style="position: absolute; bottom: 50px;">
            <AppReply @hide="replyStatus = false" />
          </div>

          <div class="options w-100 d-flex justify-content-end align-items-center px-2 my-3">
            <div class="rounded-circle p-2 mt-3 ms-3 toggle-active-deep user-select-none cursor-pointer" @click="toggleReply">
              <svg width="24" height="24" stroke-width="1.5" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M7 12L17 12" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M7 8L13 8" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M3 20.2895V5C3 3.89543 3.89543 3 5 3H19C20.1046 3 21 3.89543 21 5V15C21 16.1046 20.1046 17 19 17H7.96125C7.35368 17 6.77906 17.2762 6.39951 17.7506L4.06852 20.6643C3.71421 21.1072 3 20.8567 3 20.2895Z" stroke="currentColor" stroke-width="1.5"/>
              </svg>
            </div>

            <div class="rounded-circle p-2 mt-3 ms-3 toggle-active-deep user-select-none cursor-pointer" @click="toggleLike">
              <svg width="24" height="24" stroke-width="1.5" viewBox="0 0 24 24" :fill="likeStatus ? 'red' : 'none'" xmlns="http://www.w3.org/2000/svg">
                <path d="M22 8.86222C22 10.4087 21.4062 11.8941 20.3458 12.9929C17.9049 15.523 15.5374 18.1613 13.0053 20.5997C12.4249 21.1505 11.5042 21.1304 10.9488 20.5547L3.65376 12.9929C1.44875 10.7072 1.44875 7.01723 3.65376 4.73157C5.88044 2.42345 9.50794 2.42345 11.7346 4.73157L11.9998 5.00642L12.2648 4.73173C13.3324 3.6245 14.7864 3 16.3053 3C17.8242 3 19.2781 3.62444 20.3458 4.73157C21.4063 5.83045 22 7.31577 22 8.86222Z" stroke="currentColor" stroke-linejoin="round"/>
              </svg>
            </div>
          </div>
        </div>

        <div class="user d-flex align-items-center justify-content-between">
          <div class="mx-3">
            <div
              class="user-thumbnail d-flex justify-content-center align-items-center border border-3 rounded-circle text-brand-muted"
            >
              <b>WP</b>
            </div>
          </div>
          <div class="d-flex flex-column justify-content-end mx-3">
            <div class="user-nick-name">
              <small class="text-muted fw-medium">@FakeNickName</small>
            </div>

            <div class="user-name">
              <b>Wonder Person :)</b>
            </div>
          </div>
        </div>
      </div>

      <div class="comments container-fluid">
        <div class="row">
          <AppComment
            v-for="(comment, key) in comments"
            :key="key"
            :comment="comment"
            :depth="0"
            class="col-12 mt-3"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onBeforeMount, ref } from "vue";
import axios from "axios";

import AppComment from "./AppComment.vue";
import AppReply from "./AppReply.vue";

export default defineComponent({
  name: "AppPost",
  components: { AppComment, AppReply },
  setup: () => {
    const comments = ref<unknown>();
    const getComments = async () => {
      try {
        const response = await axios(
          `${process.env.VUE_APP_ROOT_API}/comments`
        );
        return response.status === 200 ? response.data : [];
      } catch (e) {
        return [];
      }
    };

    const replyStatus = ref<boolean>(false);
    const toggleReply = () => (replyStatus.value = !replyStatus.value);

    const likeStatus = ref<boolean>(false);
    const toggleLike = () => (likeStatus.value = !likeStatus.value);

    onBeforeMount(() => {
      getComments().then((data) => {
        comments.value = data;
      });
    });

    return {
      comments,
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
$postMaxWidth: 500px;
$userSectionHeight: 80px;
$userSectionBorderWidth: 0.25rem;
$optionsSectionHeight: 30px;

.post {
  background-color: #f0fdff;
  height: auto;
  max-width: $postMaxWidth;
  min-height: $postMaxWidth * 1.25;
  border-radius: 2rem;

  & .content {
    border: 0.38rem solid rgba(80, 80, 80, 0.8);
    border-radius: 2rem;
  }

  & .body {
    background-color: rgba(0, 0, 0, 0.1);
    border-top-left-radius: 2rem * 0.61328;
    border-top-right-radius: 2rem * 0.61328;
    height: calc(100% - $userSectionHeight);
    min-height: $postMaxWidth;
  }

  & .user {
    height: $userSectionHeight;
    border-top: $userSectionBorderWidth solid rgba(0, 0, 0, 0.15);
    border-bottom-left-radius: 2rem;
    border-bottom-right-radius: 2rem;
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

  & .options {
    height: $optionsSectionHeight;
  }
}
</style>
