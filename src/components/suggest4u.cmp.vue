<template >
  <section v-if="users">
    <div class="suggetion-header">
      <div>Suggestions For You</div>
      <span>See All</span>
    </div>
    <section class="suggestions-container">
      <div
        class="suggest-container"
        v-for="suggest in suggestUsers"
        :key="suggest._id"
      >
        <div class="flex">
          <img class="suggest-img" :src="suggest.profileImgUrl" alt="" />
          <div class="username-block-suggest">
            <div class="suggest-username">{{ suggest.username }}</div>
            <div class="suggest-for-you">Suggested for you</div>
          </div>
        </div>
        <div @click="followBtnClicked(suggest)" class="follow-suggest btn">
          Follow
        </div>
      </div>
    </section>
  </section>
</template>

<script>
import { storyService } from "../services/story.service";

export default {
  props: {
    users: Array,
  },
  data() {
    return {
      loggedInUser: null,
      suggestUsers: null,
    };
  },
  methods: {
    async followBtnClicked(suggest) {
      const loggedUserCopy = JSON.parse(JSON.stringify(this.loggedInUser));
      const suggestBy = {};
      suggestBy._id = suggest._id;
      suggestBy.username = suggest.username;
      suggestBy.profileImgUrl = suggest.profileImgUrl;

      try {
        const savedUser = await this.$store.dispatch("changeFollowStatus", {
          storyBy: suggestBy,
          editedUser: loggedUserCopy,
        });
        this.loggedInUser = savedUser;

        this.renderSuggest();
      } catch (err) {
        throw err;
      }
    },
    renderSuggest() {
      const render = [];
      this.users.forEach((userSuggest) => {
        if (
          !this.loggedInUser.following.some(
            (followingUser) => followingUser._id === userSuggest._id
          )
        )
          render.push(userSuggest);
      });
      const shortRender = render.slice(0, 5);
      this.suggestUsers = shortRender;
    },
  },
  created() {
    this.loggedInUser = this.$store.getters.getUser;
    this.renderSuggest();
  },
};
</script>

<style>
</style>