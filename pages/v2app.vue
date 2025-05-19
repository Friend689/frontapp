<template>
  <div :class="{ 'form-open': formOpen }" class="max-h-screen bg-[#111111] overflow-hidden text-white w-full z-0">
    <div class="main w-full h-full flex flex-col text-center justify-between">
      <header>
        <h1 class="text-3xl pt-12 pb-8 uppercase font-thin">jobcart.ru</h1>
      </header>

      <main>
        <div class="pb-20">
          <h2 class="text-5xl font-light mb-1">Сервер компании Jobcart</h2>
          <p class="text-sm mb-8">Переходите на сайт <a href="https://jobcart.ru" class="underline">jobcart.ru</a>
          </p>
        </div>
      </main>

      <footer class="mb-8">
        <a href="https://jobcart.ru" class="underline">© JOBCART.RU {{ new Date().getFullYear() }}
        </a>
      </footer>
      <picture class="absolute h-full w-full left-0 top-0 overflow-hidden z-[-1]">
        <img class="absolute h-full w-full object-cover "
          src="https://dev.jobcart.ru/wp-content/uploads/2022/06/mt-sample-background.jpg">
      </picture>
    </div>
    <div class="side-form flex flex-col bg-[#111111] fixed top-0 w-[308px] h-full right-[-308px]">
      <form @submit.prevent="handleLogin" class="p-5 space-y-4 opacity-50 hover:opacity-100">
        <p class="text-2xl font-bold mb-6">User Login</p>

        <UInput v-model="form.username" color="neutral" icon="i-lucide-user" placeholder="Username" class="w-full"
          :ui="{ base: 'rounded-none' }" required />

        <UInput v-model="form.password" color="neutral" icon="i-lucide-lock" type="password" placeholder="Password" class="w-full"
          :ui="{ base: 'rounded-none' }" required />

        <div class="flex justify-between">
          <ULink to="/404">Lost Password </ULink>

          <button type="submit"
            class="w-auto py-2 px-4 border-2 transition hover:bg-white hover:text-black cursor-pointer">
            Login
          </button>
        </div>
      </form>
      <div @click="formOpen = !formOpen"
        class="absolute top-[23%] left-[-54px] w-[54px] bg-[#111111] h-[54px] rounded-l-sm cursor-pointer flex items-center justify-center">
        <UIcon :name="formOpen ? 'i-lucide-lock-open' : 'i-lucide-lock'" size="30"
          class="opacity-50 hover:opacity-100 hover:scale-125 transition" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: 'landing'
});

const config = useRuntimeConfig();
const wpApiUrl = config.public.wordpressApiUrl;
const fetchData = ref<object | any>(null);

const formOpen = ref(false);
const form = ref({
  username: '',
  password: ''
});
const toast = useToast();

const showToast = () => {
  toast.add({
    title: 'Fetch Success. ' + fetchData.value.name,
    message: 'You have successfully get data. ' + fetchData.value.name,
    color: 'success'
  });
}

const handleLogin = async () => {
  console.log("send login request " + form.value.username + " " + form.value.password);
};

onMounted(async () => {
  try {
    const response = await fetch("https://dev.jobcart.ru/wp-json/test/v2/app");
    if (!response.ok) {
      throw new Error('Failed to fetch data');
    }
    const data = await response.json();
    fetchData.value = data || null;

    console.log(fetchData.value);
    showToast();
  } catch (err) {
    console.error('Error fetching:', err);
    error.value = 'Failed to load data';
  }
});
</script>

<style>
.main {
  position: initial !important;
  transition: 0.6s ease-in-out;
}

.side-form {
  transition: 0.6s ease-in-out;
}

.form-open {
  .main {
    perspective: 1000px;
    transform-origin: 0px 50%;
    transform: perspective(1920px) rotate3d(0, 1, 0, 9deg);
    backface-visibility: hidden;
  }

  .side-form {
    right: 0;
  }
}

@media screen and (max-width: 1366px) {
  .form-open {
    .main {
      transform-origin: 0px 50%;
      transform: rotateY(0deg);
    }
  }
}
</style>