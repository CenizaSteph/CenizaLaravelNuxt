<template>
	<div class="mx-auto max-w-5xl py-10">   
		<div>
			<FormButton type="button" buttonStyle="primary" @click="navigateTo('/users/add')">Add Users
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
            </svg>
            </FormButton>
		</div>
		<div>
			<div v-if="pending">
				Loading ...
			</div>
			<div v-else>
				<table class="min-w-full text-left text-sm font-light">
					<thead class="border-b font-medium dark:border-neutral-500">
						<tr>
							<th scope="col" class="px-6 py-2">Name</th>
							<th scope="col" class="px-6 py-2">Course</th>
							<th scope="col" class="px-6 py-2">Is Active</th>
							<th scope="col" class="px-6 py-2">Action</th>
						</tr>
					</thead>
					<tbody>
						<tr class="border-b dark:border-neutral-500" v-for="user in users.data">
							<td class="whitespace-nowrap px-6 py-2 font-medium">{{ user.name }}</td>
							<td class="whitespace-nowrap px-6 py-2">{{ user.course }}</td>
							<td class="whitespace-nowrap px-6 py-2">
								<span v-if="user.is_active" class="text-green-700">✓</span>
								<span v-else class="text-red-700">X</span>
							</td>
							<td class="whitespace-nowrap px-6 flex">
								<a class="cursor-pointer" @click="viewUser(user.id)">
								<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M9 4.5v15m6-15v15m-10.875 0h15.75c.621 0 1.125-.504 1.125-1.125V5.625c0-.621-.504-1.125-1.125-1.125H4.125C3.504 4.5 3 5.004 3 5.625v12.75c0 .621.504 1.125 1.125 1.125z" />
                                </svg>View
								</a>
								<a class="cursor-pointer ml-10" @click="editUser(user.id)">
								<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L10.582 16.07a4.5 4.5 0 01-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 011.13-1.897l8.932-8.931zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0115.75 21H5.25A2.25 2.25 0 013 18.75V8.25A2.25 2.25 0 015.25 6H10" />
                                </svg>Edit
								</a>
                                <a class="cursor-pointer text-red-400 ml-10" @click="deleteUser(user.id)">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M15 12H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z" />
                                </svg>Delete
								</a>
							</td>
						</tr>
					</tbody>
				</table>
			</div>
		</div>
        <div class="flex justify-between mt-8">
            <FormButton v-if="currentPage > 1" type="button" buttonStyle="primary" @click="navigateToPreviousPage">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M21 16.811c0 .864-.933 1.405-1.683.977l-7.108-4.062a1.125 1.125 0 010-1.953l7.108-4.062A1.125 1.125 0 0121 8.688v8.123zM11.25 16.811c0 .864-.933 1.405-1.683.977l-7.108-4.062a1.125 1.125 0 010-1.953L9.567 7.71a1.125 1.125 0 011.683.977v8.123z" />
                </svg>
                Previous
            </FormButton>
            <FormButton v-if="currentPage < users.last_page" type="button" buttonStyle="primary" @click="navigateToNextPage">
                Next 
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M3 8.688c0-.864.933-1.405 1.683-.977l7.108 4.062a1.125 1.125 0 010 1.953l-7.108 4.062A1.125 1.125 0 013 16.81V8.688zM12.75 8.688c0-.864.933-1.405 1.683-.977l7.108 4.062a1.125 1.125 0 010 1.953l-7.108 4.062a1.125 1.125 0 01-1.683-.977V8.688z" />
                </svg>
            </FormButton>
        </div>
	</div>
</template>
  
<script setup>

let currentPage = 1;
const perPage = 10;


const { pending, data: users } = useFetch(`http://127.0.0.1:8000/api/users?page=${currentPage}&limit=${perPage}`, {
  lazy: true
});

async function navigateToPreviousPage() {
  if (currentPage > 1) {
    currentPage--;
    await fetchUsers();
  }
}

async function navigateToNextPage() {
  if (currentPage < users.value.last_page) {
    currentPage++;
    await fetchUsers();
  }
}

async function deleteUser(userId) {
  const confirmed = confirm("Are you sure you want to delete this user?");

  if (confirmed) {
    try {
      const response = await fetch(`http://127.0.0.1:8000/api/users/${userId}`, {
        method: 'DELETE',
      });
      if (response.ok) {
        await fetchUsers();
        alert("User deleted successfully!");
      } else {
        alert("Failed to delete user.");
      }
    } catch (error) {
      console.error(error);
      alert("An error occurred while deleting the user.");
    }
  }
}

async function fetchUsers() {
  const url = `http://127.0.0.1:8000/api/users?page=${currentPage}&limit=${perPage}`;
  const response = await fetch(url);
  const responseData = await response.json();
  users.value = responseData;
}

function viewUser(userId) {
	navigateTo('/users/' + userId)
}

function editUser(userId) {
	navigateTo('/users/edit?user_id=' + userId)
}
</script>