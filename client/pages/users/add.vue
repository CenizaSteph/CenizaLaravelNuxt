<template>
    <div class="mx-auto max-w-5xl py-10">
        <h3 class="text-2xl">Add User</h3>
        <div class="py-3">
            <button @click="back()" class="border px-4 py-1 hover:bg-gray-50">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M9 15L3 9m0 0l6-6M3 9h12a6 6 0 010 12h-3" />
            </svg>
            </button>
        </div>
        <div>
            <form class="space-y-3" @submit.prevent="saveUser()">
                <div>
                    <FormLabel label="Name" />
                    <FormTextField name="name" placeholder="Name" v-model="state.user.name" />
                </div>
                <div>
                    <FormLabel label="Course" />
                    <FormTextField name="course" placeholder="Course" v-model="state.user.course" />
                </div>
                <div>
                    <FormLabel label="Email" />
                    <FormTextField name="email" placeholder="Email" v-model="state.user.email" />
                </div>
                <div>
                    <FormLabel label="Password" />
                    <FormTextField type="password" name="password" placeholder="Password" v-model="state.user.password" />
                </div>
                <div>
                    <FormLabel label="Status" />
                    <br />
                    <FormCheckbox :value="state.user.is_active ? true : false"
                        @click="state.user.is_active = !state.user.is_active" />
                </div>
                <div>
                    <FormButton type="submit" buttonStyle="primary" class="w-full">ADD
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M19 7.5v3m0 0v3m0-3h3m-3 0h-3m-2.25-4.125a3.375 3.375 0 11-6.75 0 3.375 3.375 0 016.75 0zM4 19.235v-.11a6.375 6.375 0 0112.75 0v.109A12.318 12.318 0 0110.374 21c-2.331 0-4.512-.645-6.374-1.766z" />
                    </svg>
                    </FormButton>
                </div>
            </form>
        </div>
    </div>
</template>
  
<script setup>
const state = reactive({
    user: {
        name: null,
        course: null,
        password: null,
        email: null,
        is_active: false,
    }
})

function back() {
    navigateTo('/')
}

const isFormInvalid = computed(() => {
    const { name, course, email, password } = state.user;
    return !name || !course || !email || !password;
});

function resetForm() {
    state.user.name = null;
    state.user.course = null;
    state.user.email = null;
    state.user.password = null;
    state.user.is_active = false;
    $refs.userForm.reset();
}

async function saveUser() {
    if (isFormInvalid.value) {
        alert('Please fill in all the fields.');
        return;
    }
    const payload = {
        name: state.user.name,
        email: state.user.email,
        password: state.user.password,
        course: state.user.course,
        is_active: state.user.is_active,
    }
    await $fetch(`http://127.0.0.1:8000/api/users`, {
        method: 'POST',
        body: payload
    }).then((result) => {
        if (result) {
            alert('Successfully saved.')
        }
    }).catch((error) => {
        alert('Something went wrong.')
    })
}
</script>