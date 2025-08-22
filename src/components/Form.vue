<template> 
  <div class="container mt-5"> 
    <h1 class="text-center mb-4">User Information Form</h1>
    <form @submit.prevent="submitForm">
      <!-- Username + Password -->
      <div class="row mb-3">
        <div class="col-md-6 col-sm-6">
          <label for="username" class="form-label">Username</label>
          <input
            type="text"
            class="form-control"
            id="username"
            v-model="formData.username"
            @blur="() => validateName(true)"
            @input="() => validateName(false)"
          />
          <div v-if="errors.username" class="text-danger">{{ errors.username }}</div>
        </div>

        <div class="col-md-6 col-sm-6">
          <label for="password" class="form-label">Password</label>
          <input
            type="password"
            class="form-control"
            id="password"
            v-model="formData.password"
            @blur="() => validatePassword(true)"
            @input="() => validatePassword(false)"
          />
          <div v-if="errors.password" class="text-danger">{{ errors.password }}</div>
        </div>
      </div>

      <!-- Email + Age -->
      <!-- Email -->
      <div class="mb-3">
        <label for="email" class="form-label">Email</label>
        <input
          type="email"
          id="email"
          class="form-control"
          v-model="email"
          v-bind="emailAttrs"
        />
        <div v-if="emailError" class="text-danger">{{ emailError }}</div>
      </div>

      <!-- Age -->
      <div class="mb-3">
        <label for="age" class="form-label">Age</label>
        <input
          type="number"
          id="age"
          class="form-control"
          v-model="age"
          v-bind="ageAttrs"
        />
        <div v-if="ageError" class="text-danger">{{ ageError }}</div>
      </div>



      <!-- Resident + Gender -->
      <div class="row mb-3">
        <div class="col-md-6 col-sm-6">
          <div class="form-check">
            <input
              type="checkbox"
              class="form-check-input"
              id="isAustralian"
              v-model="formData.isAustralian"
              @change="() => validateResident(true)"
            />
            <label class="form-check-label" for="isAustralian">Australian Resident?</label>
          </div>
          <div v-if="errors.resident" class="text-danger">{{ errors.resident }}</div>
        </div>

        <div class="col-md-6 col-sm-6">
          <label for="gender" class="form-label">Gender</label>
          <select
            class="form-select"
            id="gender"
            v-model="formData.gender"
            @change="() => validateGender(true)"
          >
            <option value="">Select</option>
            <option value="male">Male</option>
            <option value="female">Female</option>
            <option value="other">Other</option>
          </select>
          <div v-if="errors.gender" class="text-danger">{{ errors.gender }}</div>
        </div>
      </div>

      <!-- Reason -->
      <div class="mb-3">
        <label for="reason" class="form-label">Reason for joining</label>
        <textarea
          class="form-control"
          id="reason"
          rows="3"
          v-model="formData.reason"
          @blur="() => validateReason(true)"
          @input="() => validateReason(false)"
        ></textarea>
        <div v-if="errors.reason" class="text-danger">{{ errors.reason }}</div>
      </div>

      <!-- Submit / Clear -->
      <div class="text-center">
        <button type="submit" class="btn btn-primary me-2">Submit</button>
        <button type="button" class="btn btn-secondary" @click="clearForm">Clear</button>
      </div>
    </form>

    <!-- DataTable -->
    <DataTable
      v-if="submittedCards.length"
      :value="submittedCards"
      class="mt-5"
      responsiveLayout="scroll"
      stripedRows
    >
      <Column field="username" header="Username" />
      <Column field="password" header="Password" />
      <Column header="Resident">
        <template #body="slotProps">
          {{ slotProps.data.isAustralian ? 'Yes' : 'No' }}
        </template>
      </Column>
      <Column field="gender" header="Gender" />
      <Column field="reason" header="Reason" />
      <Column field="email" header="Email" />
      <Column field="age" header="Age" />
    </DataTable>
  </div>
</template>


<script setup>
import { ref } from 'vue'

const formData = ref({
  username: '',
  password: '',
  isAustralian: false,
  gender: '',
  reason: '',
  email: '',
  age: null
})

const submittedCards = ref([])

const errors = ref({
  username: null,
  password: null,
  resident: null,
  gender: null,
  reason: null,
  email: null,
  age: null
})

// --- Validation functions ---

const validateName = (blur) => {
  if (formData.value.username.length < 3) {
    if (blur) errors.value.username = 'Name must be at least 3 characters'
  } else {
    errors.value.username = null
  }
}

const validatePassword = (blur) => {
  const password = formData.value.password
  const minLength = 8
  const hasUppercase = /[A-Z]/.test(password)
  const hasLowercase = /[a-z]/.test(password)
  const hasNumber = /\d/.test(password)
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password)

  if (password.length < minLength) {
    if (blur) errors.value.password = `Password must be at least ${minLength} characters long.`
  } else if (!hasUppercase) {
    if (blur) errors.value.password = 'Password must contain at least one uppercase letter.'
  } else if (!hasLowercase) {
    if (blur) errors.value.password = 'Password must contain at least one lowercase letter.'
  } else if (!hasNumber) {
    if (blur) errors.value.password = 'Password must contain at least one number.'
  } else if (!hasSpecialChar) {
    if (blur) errors.value.password = 'Password must contain at least one special character.'
  } else {
    errors.value.password = null
  }
}

const validateResident = (blur) => {
  if (!formData.value.isAustralian) {
    if (blur) errors.value.resident = 'You must be an Australian resident.'
  } else {
    errors.value.resident = null
  }
}

const validateGender = (blur) => {
  if (!formData.value.gender) {
    if (blur) errors.value.gender = 'Please select a gender.'
  } else {
    errors.value.gender = null
  }
}

const validateReason = (blur) => {
  if (!formData.value.reason || formData.value.reason.length < 10) {
    if (blur) errors.value.reason = 'Reason must be at least 10 characters long.'
  } else {
    errors.value.reason = null
  }
}



// const validateEmail = (blur) => {
//   const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
//   if (!emailRegex.test(formData.value.email)) {
//     if (blur) errors.value.email = 'Please enter a valid email address.'
//   } else {
//     errors.value.email = null
//   }
// }

// const validateAge = (blur) => {
//   const age = Number(formData.value.age)
//   if (!age || isNaN(age) || age < 18 || age > 100) {
//     if (blur) errors.value.age = 'Age must be a number between 18 and 100.'
//   } else {
//     errors.value.age = null
//   }
// }




// createValidator insstead of validate functions
const [email, emailError, emailAttrs] = createValidator(
  '',
  (value) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) ? true : 'Please enter a valid email address.'
)

const [age, ageError, ageAttrs] = createValidator(
  '',
  (value) => {
    const num = Number(value)
    return num >= 18 && num <= 100
      ? true
      : 'Age must be a number between 18 and 100.'
  }
)


// --- Form submission & clear ---
const submitForm = () => {
  emailAttrs.onBlur()
  ageAttrs.onBlur()

  validateName(true)
  validatePassword(true)
  validateResident(true)
  validateGender(true)
  validateReason(true)

  if (
    !errors.value.username &&
    !errors.value.password &&
    !errors.value.resident &&
    !errors.value.gender &&
    !errors.value.reason &&
    emailError.value === undefined &&
    ageError.value === undefined
  ) {
    submittedCards.value.push({
      ...formData.value,
      email: email.value,
      age: age.value
    })
    clearForm()
  }
}


const clearForm = () => {
  formData.value = {
    username: '',
    password: '',
    isAustralian: false,
    gender: '',
    reason: ''
  }

  email.value = ''
  age.value = ''
  emailError.value = undefined
  ageError.value = undefined

  errors.value = {
    username: null,
    password: null,
    resident: null,
    gender: null,
    reason: null
  }
}




function createValidator(initialValue, validate, onlyValidatesOnBlur = false) {
  const value = ref(initialValue)
  const error = ref(undefined)

  const runValidation = () => {
    const result = validate(value.value)
    error.value = result === true ? undefined : result
  }

  const attrs = {
    onInput: (event) => {
      value.value = event.target.value
      if (!onlyValidatesOnBlur) runValidation()
    },
    onBlur: () => runValidation()
  }

  return [value, error, attrs]
}


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