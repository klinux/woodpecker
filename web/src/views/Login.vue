<template>
  <main class="flex flex-col w-full h-full justify-center items-center">
    <!-- TODO: Should use vue notifications. -->
    <div
      v-if="errorMessage"
      class="bg-wp-control-error-100 border-l-6 border-l-wp-control-error-300 text-white p-4 rounded-md"
    >
      {{ errorMessage }}
    </div>

    <div
      class="flex flex-col w-full overflow-hidden md:m-8 md:rounded-md md:shadow md:border md:border-wp-background-400 md:bg-wp-background-100 md:dark:bg-wp-background-200 md:flex-row md:w-3xl md:h-sm justify-center"
    >
      <div class="flex md:bg-wp-primary-200 md:dark:bg-wp-primary-300 md:w-3/5 justify-center items-center">
        <WoodpeckerLogo class="w-48 h-48" />
      </div>
      <div class="flex flex-col my-8 md:w-2/5 p-4 items-center justify-center">
        <h1 class="text-xl text-wp-text-100">{{ $t('welcome') }}</h1>
        <Button class="mt-4" @click="doLogin">{{ $t('login') }}</Button>
      </div>
    </div>
  </main>
</template>

<script lang="ts" setup>
import { onMounted, ref } from 'vue';
import { useI18n } from 'vue-i18n';
import { useRoute, useRouter } from 'vue-router';

import WoodpeckerLogo from '~/assets/logo.svg?component';
import Button from '~/components/atomic/Button.vue';
import useAuthentication from '~/compositions/useAuthentication';

const route = useRoute();
const router = useRouter();
const authentication = useAuthentication();
const errorMessage = ref<string>();
const i18n = useI18n();

function doLogin() {
  const url = typeof route.query.url === 'string' ? route.query.url : '';
  authentication.authenticate(url);
}

const authErrorMessages = {
  oauth_error: i18n.t('user.oauth_error'),
  internal_error: i18n.t('user.internal_error'),
  access_denied: i18n.t('user.access_denied'),
};

onMounted(async () => {
  if (authentication.isAuthenticated) {
    await router.replace({ name: 'home' });
    return;
  }

  if (route.query.code) {
    const code = route.query.code as keyof typeof authErrorMessages;
    errorMessage.value = authErrorMessages[code];
  }
});
</script>
