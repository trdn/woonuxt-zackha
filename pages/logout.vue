<template>
  <div class="container flex min-h-[300px] text-2xl items-center justify-center">
    <LoadingIcon v-if="hasLoggedOut == null" />
    <h2 v-if="hasLoggedOut == true">You have been successfully logged out.</h2>
    <h2 v-if="hasLoggedOut == false">Something went wrong. Please try again</h2>
  </div>
</template>

<script>
import { gql } from "nuxt-graphql-request";

export default {
  data() {
    return {
      hasLoggedOut: null,
    };
  },
  methods: {
    async logOut() {
      // generate random token
      const token = Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);

      const query = gql`
				mutation Logout {
					logout(input: { clientMutationId: ${token} }) { status }
				}
			`;

      const { logout } = await this.$graphql.default.request(query);
      this.hasLoggedOut = logout.status == "SUCCESS" ? true : false;

      if (this.hasLoggedOut) {
        this.$store.commit("updateUser", undefined);
        this.$store.commit("updateCart", null);
        this.$cookiz.removeAll();
        setTimeout(() => {
          this.$router.push("/");
        }, 500);
      }
    },
  },
  mounted() {
    console.log(this.$store.state.user);
    this.$store.state.user ? this.logOut() : this.$router.push("/");
  },
};
</script>
