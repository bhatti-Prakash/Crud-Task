<template>
  <div class="container mx-auto">
    <div class="bg-black-800 shadow-md rounded my-6 p-4 sm:p-6 overflow-x-auto">
      <div class="flex flex-col sm:flex-row items-center justify-between mb-4">
        <div class="relative w-full sm:w-auto mb-4 sm:mb-0">
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center">
            <svg class="w-4 h-4 fill-current" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
              <path fill-rule="evenodd"
                d="M11.805 14.805a8 8 0 111.415-1.414l4.243 4.243a1 1 0 01-1.414 1.414l-4.244-4.244zM14 10a4 4 0 11-8 0 4 4 0 018 0z"
                clip-rule="evenodd" />
            </svg>
          </div>
          <input v-model="searchQuery" type="text"
            class="w-full h-10 pl-10 pr-3 text-base placeholder-gray-500 border rounded-lg focus:shadow-outline"
            placeholder="Search by user name" />
        </div>

        <button type="button" @click="openDialog()"
          class="inline-flex justify-center px-4 py-2 text-sm font-medium text-white bg-blue-500 border border-transparent rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
          Create User
        </button>
      </div>
      <table class="min-w-max w-full table-auto">
        <thead>
          <tr class="bg-gray-200 text-gray-600 uppercase text-sm leading-normal">
            <!-- <th class="py-3 px-6 text-left">ID</th> -->
            <th class="py-3 px-6 text-left">USER NAME</th>
            <th class="py-3 px-6 text-left">EMAIL</th>
            <th class="py-3 px-6 text-left">DATE</th>
            <th class="py-3 px-6 text-left">STAUS ORDER</th>
            <th class="py-3 px-6 text-left">TOTAL PRICE</th>
            <th class="py-3 px-6 text-left">ORDER ACTIONS</th>
          </tr>
        </thead>
        <tbody class="text-black-800 text-sm font-normal" v-if="paginatedData.length > 0">
          <tr v-for="item in paginatedData" :key="item.id" class="border-b border-gray-200 hover:bg-gray-100">
            <!-- <td class="py-3 px-6 text-left">{{ item.id }}</td> -->
            <td class="py-3 px-6 text-left">{{ item.username }}</td>
            <td class="py-3 px-6 text-left">{{ item.email }}</td>
            <td class="py-3 px-6 text-left">{{ formatDate(item.date) }}</td>
            <td class="py-3 px-6 text-center">
              <div class="border rounded"
                :class="{ 'bg-green-100': item.status === 'Completed', 'bg-red-100': item.status === 'Rejected', 'bg-orange-100': item.status === 'Pending' }">
                <p
                  :class="{ 'text-green-800': item.status === 'Completed', 'text-red-800': item.status === 'Rejected', 'text-orange-800': item.status === 'Pending' }">
                  {{ item.status }}</p>
              </div>
            </td>
            <td class="py-3 px-6 text-left">{{ '$' + item.totalprice }}</td>
            <td class="py-3 px-6 text-left"> <button @click="openDialog(item), editId = item.id"
                class="text-blue-500 underline">Edit order</button></td>
          </tr>
        </tbody>
        <tbody v-else>
          <tr>
            <td colspan="7" class="py-3 px-6 text-center">
              <p class="inline-flex justify-center text-lg">No data found.</p>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <!-- dailog -->
    <div v-if="showDialog" class="fixed z-10 inset-0 overflow-y-auto">
      <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
        <div class="fixed inset-0 transition-opacity" aria-hidden="true">
          <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
        </div>

        <div
          class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
          <!-- Header -->
          <div class="bg-gray-100 px-4 py-2 flex justify-between items-center">
            <h3 class="text-lg leading-6 font-medium text-gray-900">{{ title }} User</h3>
            <button @click="closeDialog" type="button"
              class="text-gray-500 hover:text-gray-600 focus:outline-none focus:text-gray-600">
              <svg class="h-6 w-6" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"
                stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
          </div>

          <!-- Content -->
          <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
            <div class="mt-4">
              <label for="name" class="block text-sm font-medium text-gray-700">Name</label>
              <input type="text" name="name" id="name" v-model="userName" @input="checkValidation"
                placeholder="Enter user name"
                class="mt-1 ring-indigo-500 border border-gray-300 h-8 block w-full shadow-sm sm:text-sm rounded-md">
              <p v-if="!isNameValid" class="mt-1 text-red-500 text-sm">Please enter a valid name.</p>
            </div>
            <div class="mt-4">
              <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
              <input type="email" name="email" id="email" v-model="email" @input="checkValidation"
                placeholder="Enter email"
                class="mt-1 ring-indigo-500 border border-gray-300 h-8 block w-full shadow-sm sm:text-sm rounded-md">
              <p v-if="!isEmailValid" class="mt-1 text-red-500 text-sm">Please enter a valid email.</p>
            </div>
            <div class="mt-4">
              <label for="date" class="block text-sm font-medium text-gray-700">Date</label>
              <input type="date" name="date" id="date" v-model="date" @input="checkValidation" placeholder="Select date"
                class="mt-1 ring-indigo-500 border border-gray-300 h-8 block w-full shadow-sm sm:text-sm rounded-md">
              <p v-if="!isDateValid" class="mt-1 text-red-500 text-sm">Please enter a valid date.</p>
            </div>
            <div class="mt-4">
              <label for="status" class="block text-sm font-medium text-gray-700">Status of order</label>
              <select name="status" id="status" v-model="status" @change="checkValidation"
                class="mt-1 ring-indigo-500 border border-gray-300 h-8 block w-full shadow-sm sm:text-sm rounded-md">
                <option value="" selected>Select status</option>
                <option value="Completed">Completed</option>
                <option value="Pending">Pending</option>
                <option value="Rejected">Rejected</option>
              </select>
              <p v-if="!isStatusValid" class="mt-1 text-red-500 text-sm">Please select a status.</p>
            </div>
            <div class="mt-4">
              <label for="price" class="block text-sm font-medium text-gray-700">Total price</label>
              <input type="number" max="5" name="price" id="price" v-model="totalprice" @input="checkValidation"
                placeholder="Enter total price"
                class="mt-1 ring-indigo-500 border border-gray-300 h-8 block w-full shadow-sm sm:text-sm rounded-md">
              <p v-if="!isPriceValid" class="mt-1 text-red-500 text-sm">Please enter a valid price (max 5).</p>
            </div>
            <div class="mt-4">
              <button type="button"
                class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-blue-500 text-base font-medium text-white hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 sm:ml-3 sm:w-auto sm:text-sm"
                @click="saveUser()">
                {{ title }} user</button>
            </div>
            <!-- Footer -->
            <div class="mt-5 sm:mt-6  px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
              <button @click="closeDialog" type="button"
                class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-blue-500 text-base font-medium text-white hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 sm:ml-3 sm:w-auto sm:text-sm">
                Close
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Pagination -->
    <div class="flex justify-center mt-4">
      <button @click="previousPage" :disabled="currentPage === 1"
        class="mr-2 px-4 py-2 bg-gray-300 text-gray-600 rounded-md disabled:opacity-50">Previous</button>
      <template v-for="pageNumber in pageNumbers">
        <button @click="goToPage(pageNumber)"
          :class="{ 'bg-blue-500 text-white': pageNumber === currentPage, 'bg-gray-300 text-gray-600': pageNumber !== currentPage }"
          class="mx-2 px-4 py-2 rounded-md">{{ pageNumber }}</button>
      </template>
      <button @click="nextPage" :disabled="currentPage === totalPages"
        class="ml-2 px-4 py-2 bg-gray-300 text-gray-600 rounded-md disabled:opacity-50">Next</button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import moment from 'moment';
import { ref, onMounted, computed, watch, type Ref } from 'vue';
interface DataObject {
  id: number;
  username: string;
  email: string;
  date: string;
  status: string;
  totalprice: number;
}
const userName = ref('');
const email = ref('');
const date = ref('');
const status = ref('');
const totalprice = ref('');
const editId = ref(0);
const editData = ref({});
const isNameValid = ref(true);
const isEmailValid = ref(true);
const isDateValid = ref(true);
const isStatusValid = ref(true);
const isPriceValid = ref(true);
const title=ref('Create');
const datatable: Ref<DataObject[]> = ref([
  {
    id: 1,
    username: 'John Doe',
    email: 'john@example.com',
    date: '2024-02-22',
    status: 'Completed',
    totalprice: 100.00
  },
  {
    id: 2,
    username: 'Jane Smith',
    email: 'jane@example.com',
    date: '2024-02-21',
    status: 'Pending',
    totalprice: 75.50
  },
  {
    id: 3,
    username: 'Bob Johnson',
    email: 'bob@example.com',
    date: '2024-02-20',
    status: 'Rejected',
    totalprice: 200.00
  },
  {
    id: 4,
    username: 'Alice Johnson',
    email: 'alice@example.com',
    date: '2024-02-19',
    status: 'Completed',
    totalprice: 150.75
  },
  {
    id: 5,
    username: 'John Doe',
    email: 'john@example.com',
    date: '2024-02-22',
    status: 'Completed',
    totalprice: 100.00
  },
  {
    id: 6,
    username: 'Jane Smith',
    email: 'jane@example.com',
    date: '2024-02-21',
    status: 'Pending',
    totalprice: 75.50
  },
  {
    id: 7,
    username: 'Bob Johnson',
    email: 'bob@example.com',
    date: '2024-02-20',
    status: 'Rejected',
    totalprice: 200.00
  },
  {
    id: 8,
    username: 'Alice Johnson',
    email: 'alice@example.com',
    date: '2024-02-19',
    status: 'Completed',
    totalprice: 150.75
  },
  {
    id: 9,
    username: 'John Doe',
    email: 'john@example.com',
    date: '2024-02-22',
    status: 'Completed',
    totalprice: 100.00
  },
  {
    id: 10,
    username: 'Jane Smith',
    email: 'jane@example.com',
    date: '2024-02-21',
    status: 'Pending',
    totalprice: 75.50
  },
  {
    id: 11,
    username: 'Bob Johnson',
    email: 'bob@example.com',
    date: '2024-02-20',
    status: 'Rejected',
    totalprice: 200.00
  },
  {
    id: 12,
    username: 'Alice Johnson',
    email: 'alice@example.com',
    date: '2024-02-19',
    status: 'Completed',
    totalprice: 150.75
  },
  {
    id: 13,
    username: 'John Doe',
    email: 'john@example.com',
    date: '2024-02-22',
    status: 'Completed',
    totalprice: 100.00
  },
  {
    id: 14,
    username: 'Jane Smith',
    email: 'jane@example.com',
    date: '2024-02-21',
    status: 'Pending',
    totalprice: 75.50
  },
  {
    id: 15,
    username: 'Bob Johnson',
    email: 'bob@example.com',
    date: '2024-02-20',
    status: 'Rejected',
    totalprice: 200.00
  },
  {
    id: 16,
    username: 'Alice Johnson',
    email: 'alice@example.com',
    date: '2024-02-19',
    status: 'Completed',
    totalprice: 150.75
  },
]);
const formatDate=(inputDate: string): string =>{
    const formattedDate = moment(inputDate).format("MMM DD, YYYY");
    return formattedDate;
}
onMounted(() => localStorage.setItem("user", JSON.stringify(datatable.value)))
const showDialog = ref(false);
const openDialog = (item?: DataObject) => {
  if (item) {
    title.value='Edit'
    editData.value = item;
    editId.value = item.id;
    userName.value = item.username;
    email.value = item.email;
    date.value = item.date;
    status.value = item.status;
    totalprice.value = String(item.totalprice);
  } else {

    resetDailog();
  }
  showDialog.value = true;
};
const resetDailog = () => {
  title.value='Create'
  userName.value = '';
  email.value = '';
  date.value = '';
  status.value = '';
  totalprice.value = '';
  editData.value = {};
  editId.value = 0;
  isNameValid.value = true;
  isEmailValid.value = true;
  isStatusValid.value = true;
  isDateValid.value = true;
  isPriceValid.value = true;
}
const closeDialog = () => {
  showDialog.value = false;
  resetDailog();
};
const checkValidation = () => {
  isNameValid.value = userName.value.trim() !== '';
  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  isEmailValid.value = emailPattern.test(email.value);
  isDateValid.value = date.value.trim() !== '';
  isStatusValid.value = status.value.trim() !== '';
  isPriceValid.value = /^\d{1,5}(\.\d{1,2})?$/.test(totalprice.value);
  if (isNameValid.value == true && isEmailValid.value == true && isDateValid.value == true && isStatusValid.value == true && isPriceValid.value == true) {
    return true
  } else {
    return false
  }
}
const saveUser = () => {
  let ischeck = checkValidation();
  if (ischeck == true) {
    if (editId.value != 0 && Object.keys(editData).length > 0) {
      const data = {
        id: editId.value,
        username: userName.value,
        email: email.value,
        date: date.value,
        status: status.value,
        totalprice: Number(totalprice.value)
      }
      const index = datatable.value.findIndex(item => item.id === editId.value);
      datatable.value[index] = { ...datatable.value[index], ...data };
      localStorage.setItem('user', JSON.stringify(datatable.value));
      closeDialog();
    } else {
      const id = datatable.value.reduce((maxId, item) => Math.max(maxId, item.id), 0);
      const data = {
        id: id + 1,
        username: userName.value,
        email: email.value,
        date: date.value,
        status: status.value,
        totalprice: Number(totalprice.value)
      }
      datatable.value.push(data);
      localStorage.setItem('user', JSON.stringify(datatable.value));
      closeDialog();
    }
  }
}

const searchQuery = ref('');

const filteredData = computed(() => {
  const query = searchQuery.value.trim().toLowerCase();
  return datatable.value.filter(item => item.username.toLowerCase().includes(query));
});
watch(() => searchQuery.value, () => {
  const query = searchQuery.value.trim().toLowerCase();
  return datatable.value.filter(item => item.username.toLowerCase().includes(query));
});


const pageSize = ref(5);
const currentPage = ref(1);

const totalPages = computed(() => Math.ceil(datatable.value.length / pageSize.value));
const paginatedData = computed(() => {
  const startIndex = (currentPage.value - 1) * pageSize.value;
  return filteredData.value.slice(startIndex, startIndex + pageSize.value);
});
const pageNumbers = computed(() => {
  const pages = [];
  for (let i = 1; i <= totalPages.value; i++) {
    pages.push(i);
  }
  return pages;
});

const previousPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

const goToPage = (pageNumber: number) => {
  currentPage.value = pageNumber;
};
</script>

<style scoped></style>
