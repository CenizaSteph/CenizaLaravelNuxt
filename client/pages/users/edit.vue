<template>
    <div class="mx-auto max-w-5xl py-10">
        <h3 class="text-2xl">Edit User</h3>
        <div class="py-3">
            <button @click="back()" class="border px-4 py-1 hover:bg-gray-50">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9 15L3 9m0 0l6-6M3 9h12a6 6 0 010 12h-3" />
            </svg>
            </button>
        </div>
        <div v-if="pending">
            Loading...
        </div>
        <div v-else>
            <form class="space-y-3" @submit.prevent="updateUser()">
                <div>
                    <FormLabel label="Name" />
                    <FormTextField name="name" placeholder="Name" v-model="state.user.name" />
                </div>
                <div>
                    <FormLabel label="Course" />
                    <FormTextField name="course" placeholder="Course" v-model="state.user.course" />
                </div>
                <div>
                    <FormLabel label="Status" />
                    <br />
                    <FormCheckbox :value="state.user.is_active ? true : false"
                        @click="state.user.is_active = !state.user.is_active" />
                </div>
                <div>
                    <FormButton type="submit" buttonStyle="primary" class="w-full">UPDATE
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M16.023 9.348h4.992v-.001M2.985 19.644v-4.992m0 0h4.992m-4.993 0l3.181 3.183a8.25 8.25 0 0013.803-3.7M4.031 9.865a8.25 8.25 0 0113.803-3.7l3.181 3.182m0-4.991v4.99" />
                    </svg>
                    </FormButton>
                </div>
            </form>
        </div>
    </div>
</template>
  
<script setup>
const route = useRoute();
const userId = route.query.user_id

const { pending, data: user } = await useLazyAsyncData('user', () => $fetch(`http://127.0.0.1:8000/api/users/${userId}`))

const state = reactive({
    user: {
        name: user && user.value && user.value.name,
        course: user && user.value && user.value.course,
        is_active: user && user.value && user.value.is_active,
    }
})

watch(user, (newData) => {
    state.user = {
        name: newData && newData.name,
        course: newData && newData.course,
        is_active: newData && newData.is_active,
    }
})

function back() {
    navigateTo('/')
}

async function updateUser() {
    const payload = {
        name: state.user.name,
        course: state.user.course,
        is_active: state.user.is_active,
    }
    await $fetch(`http://127.0.0.1:8000/api/users/${userId}`, {
        method: 'PUT',
        body: payload
    }).then((result) => {
        if (result) {
            alert('Successfully updated.')
        }
    })
}
</script>