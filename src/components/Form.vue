<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-8 offset-md-2">
        <h1 class="text-center">User Information Form</h1>
        <form @submit.prevent="submitForm">
          <div class="row mb-3">
            <div class="col-sm-6 col-md-6">
              <label for="username" class="form-label">Username</label>
              <input type="text" class="form-control" id="username" 
                @blur="() => validateName(true)"
                @input="() => validateName(false)"
                v-model="formData.username">
              <div v-if="errors.username" class="text-danger">{{ errors.username }}</div>
            </div>
            <div class="col-sm-6 col-md-6">
              <label for="password" class="form-label">Password</label>
              <input type="password" class="form-control" id="password" 
                @blur="() => validatePassword(true)"
                @input="() => validatePassword(false)"
                v-model="formData.password">
              <div v-if="errors.password" class="text-danger">{{ errors.password }}</div>
            </div>
          </div>
          <div class="row mb-3">
            <div class="col-md-6">
              <div class="form-check">
                <input type="checkbox" class="form-check-input" id="isAustralian"
                  @blur="() => validateResident(true)"
                  @input="() => validateResident(false)"
                  v-model="formData.isAustralian">
                <label class="form-check-label" for="isAustralian">Australian Resident?</label>
                <div v-if="errors.resident" class="text-danger">{{ errors.resident }}</div>
              </div>
            </div>
            <div class="col-md-6">
              <label for="gender" class="form-label">Gender</label>
              <select class="form-select" id="gender"
                @blur="() => validateGender(true)"
                @input="() => validateGender(false)"
                v-model="formData.gender">
                <option disabled value="">Please select</option>
                <option value="male">Male</option>
                <option value="female">Female</option>
                <option value="other">Other</option>
              </select>
              <div v-if="errors.gender" class="text-danger">{{ errors.gender }}</div>
            </div>
          </div>
          <div class="mb-3">
            <label for="reason" class="form-label">Reason for joining</label>
            <textarea class="form-control" id="reason" rows="3" 
              @blur="() => validateReason(true)"
              @input="() => validateReason(false)"
              v-model="formData.reason"></textarea>
          </div>
          <div v-if="errors.reason" class="text-danger">{{ errors.reason }}</div>
          <div class="text-center">
            <button type="submit" class="btn btn-primary me-2">Submit</button>
            <button type="button" class="btn btn-secondary" @click="clearForm">Clear</button>
          </div>
        </form>
        <!-- <div class="row mt-5" v-if="submittedCards.length">
          <div class="d-flex flex-wrap justify-content-start">
            <div v-for="(card, index) in submittedCards" :key="index" class="card m-2" style="width: 18rem;">
              <div class="card-header">
                User Information
              </div>
              <ul class="list-group list-group-flush">
                <li class="list-group-item">Username: {{ card.username }}</li>
                <li class="list-group-item">Password: {{ card.password }}</li>
                <li class="list-group-item">Australian Resident: {{ card.isAustralian ? 'Yes' : 'No' }}</li>
                <li class="list-group-item">Gender: {{ card.gender }}</li>
                <li class="list-group-item">Reason: {{ card.reason }}</li>
              </ul>
            </div>
          </div>
        </div> -->
      </div>
    </div>
  <DataTable :value="submittedCards" tableStyle="min-width: 50rem" class="mt-5">
    <Column field="username" header="Username"></Column>
    <Column field="password" header="Password"></Column>
    <Column field="isAustralian" header="Australian Resident">
      <template #body="{ data }">
        {{ data.isAustralian ? 'Yes' : 'No' }}
      </template>
    </Column>
    <Column field="gender" header="Gender"></Column>
    <Column field="reason" header="Reason"></Column>
  </DataTable>
  </div>
</template>


<script setup>
import { ref } from 'vue';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
import ColumnGroup from 'primevue/columngroup';   
import Row from 'primevue/row';  
const formData = ref({
    username: '',
    password: '',
    isAustralian: false,
    reason: '',
    gender: ''
});

const submittedCards = ref([]);

const submitForm = () => {
    validateName(true);
    validatePassword(true);
    if (!errors.value.username && !errors.value.password && !errors.value.resident && !errors.value.gender && !errors.value.reason) {
      submittedCards.value.push({...formData.value});
      clearForm();
  }
};

const clearForm = () => {
  formData.value.username = ''
  formData.value.password = ''
  formData.value.isAustralian = false
  formData.value.gender = ''
  formData.value.reason = ''
}

const errors = ref({
  username: null,
  password: null,
  resident: null,
  gender: null,
  reason: null,
})

const validateName = (blur) => {
  if (formData.value.username.length < 3) {
    errors.value.username = 'Username must be at least 3 characters';
  } else {
    errors.value.username = null;
  }
}

const validatePassword = (blur) => {
  const password = formData.value.password;
  const minLength = 8;
  const hasUppercase = /[A-Z]/.test(password);
  const hasLowercase = /[a-z]/.test(password);
  const hasNumber = /\d/.test(password);
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);

  if (password.length < minLength) {
    if (blur) errors.value.password = `Password must be at least ${minLength} characters long.`;
  } else if (!hasUppercase) {
    if (blur) errors.value.password = "Password must contain at least one uppercase letter.";
  } else if (!hasLowercase) {
    if (blur) errors.value.password = "Password must contain at least one lowercase letter.";
  } else if (!hasNumber) {
    if (blur) errors.value.password = "Password must contain at least one number.";
  } else if (!hasSpecialChar) {
    if (blur) errors.value.password = "Password must contain at least one special character.";
  } else {
    errors.value.password = null;
  }
};

const validateResident = (blur) => {
  if (!formData.value.isAustralian) {
    errors.value.resident = 'You must be an Australian resident to submit this form.';
  } else {
    errors.value.resident = null;
  }
};

const validateGender = (blur) => {
  if (!formData.value.gender) {
    errors.value.gender = 'Gender is required.';
  } else {
    errors.value.gender = null;
  }
};

const validateReason = (blur) => {
  if (!formData.value.reason) {
    errors.value.reason = 'Reason for joining is required.';
  } else if (formData.value.reason.length < 10) {
    errors.value.reason = 'Reason must be at least 10 characters.';
  } else if (formData.value.reason.length > 20) {
    errors.value.reason = 'Reason must be less than 20 characters.';
  } else {
    errors.value.reason = null;
  }
};
</script>

<style scoped>
   .card {
   border: 1px solid #ccc;
   border-radius: 10px;
   box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
   }
   .card-header {
   background-color: #275FDA;
   color: white;
   padding: 10px;
   border-radius: 10px 10px 0 0;
   }
   .list-group-item {
   padding: 10px;
   }
</style>