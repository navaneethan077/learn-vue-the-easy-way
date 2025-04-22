<template>
  <div id="employee-form">
    <form @submit.prevent="handleSubmit" novalidate> 
      <div class="form-group">
        <label for="employee-name">Employee Name</label>
        <input 
          id="employee-name" 
          type="text" 
          v-model.trim="employee.name" 
          :class="{'has-error': submitting && invalidName}" 
          @focus="clearStatus"
          @input="clearStatus" 
          ref="firstInput"
          placeholder="e.g., John Doe"
          aria-required="true" 
          :aria-invalid="submitting && invalidName"
        />
        <p v-if="submitting && invalidName" class="error-text">Name is required.</p>
      </div>

      <div class="form-group">
        <label for="employee-email">Employee Email</label>
        <input 
          id="employee-email"
          type="email"  
          v-model.trim="employee.email" 
          :class="{'has-error': submitting && invalidEmail}" 
          @focus="clearStatus"
          @input="clearStatus" 
          placeholder="e.g., john.doe@example.com"
          aria-required="true"
          :aria-invalid="submitting && invalidEmail"
        />
         <p v-if="submitting && invalidEmail && !employee.email" class="error-text">Email is required.</p>
         <p v-if="submitting && invalidEmail && employee.email && !isValidEmailFormat" class="error-text">Please enter a valid email format.</p>
      </div>
      
      <p v-if="error && !(invalidName || invalidEmail)" class="error-message">
        An error occurred. Please try again. 
        </p>
      <p v-if="success" class="success-message">
        Employee successfully added!
      </p>

      <button type="submit" :disabled="submitting">
        {{ submitting ? 'Adding...' : 'Add Employee' }}
      </button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'employee-form',
  data() {
    return {
      submitting: false,
      success: false,
      error: false, // More general error flag
      employee: {
        name: '',
        email: ''
      }
    }
  },
  methods: {
    handleSubmit() {
      this.submitting = true;
      this.clearStatus(); // Clear previous success/error messages but not validation flags initially

      // Perform validation check
      if (this.invalidName || this.invalidEmail) {
        this.error = true; // Indicate validation error occurred
        this.submitting = false; // Stop submission process
        // Optionally focus the first invalid field
         if (this.invalidName) {
           this.$refs.firstInput.focus();
         } else if (this.invalidEmail) {
           // Need a ref on the email input to focus it specifically
           // this.$refs.emailInput.focus(); 
         }
        return; 
      }

      // --- If Validation Passes ---
      console.log('Submitting:', this.employee); 
      // Simulate API call or data processing
      // In a real app, you might have async logic here
      // For now, assume immediate success

      this.$emit('add:employee', { ...this.employee }); // Emit a copy
      this.$refs.firstInput.focus(); // Focus name field for next entry

      // Reset form
      this.employee = {
        name: '',
        email: ''
      };
      
      this.success = true; 
      this.error = false; // Ensure no general error lingers
      // Keep submitting true briefly for visual feedback, then reset
      setTimeout(() => {
         this.submitting = false; 
         // Optionally clear success message after a delay
         // setTimeout(() => this.success = false, 3000); 
      }, 300); // Short delay to show button state change
    },

    clearStatus() {
      // Clear general success/error messages when user starts typing
      this.success = false;
      this.error = false; 
      // We don't clear submitting flag here, only on submit start/end
      // Validation classes (.has-error) depend on computed props which react automatically
    }
  },
  computed: {
    invalidName() {
      return this.employee.name === '';
    },
    isValidEmailFormat() {
       // Basic email regex - adjust if needed for stricter validation
       const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
       return emailRegex.test(this.employee.email);
    },
    invalidEmail() {
        // Invalid if empty OR if not empty but wrong format
      return this.employee.email === '' || !this.isValidEmailFormat;
    }
  }
}
</script>

<style scoped>
/* Scoped styles only apply to this component */

#employee-form {
  /* Provides padding and centers the form on larger screens */
  max-width: 500px; /* Adjust max-width as needed */
  margin: 2rem auto; /* Centers the form horizontally */
  padding: 1.5rem;  /* Padding inside the form container */
  background-color: #f9f9f9; /* Light background for the form area */
  border-radius: 8px; /* Rounded corners */
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1); /* Subtle shadow */
}

.form-group {
  margin-bottom: 1.25rem; /* Space between form fields */
}

label {
  display: block; /* Makes label take its own line */
  margin-bottom: 0.5rem; /* Space between label and input */
  font-weight: 600; /* Slightly bolder labels */
  color: #333; /* Darker label text */
}

input[type="text"],
input[type="email"] {
  display: block; /* Take full width */
  width: 100%;    /* Ensure input takes full width of container */
  padding: 0.75rem; /* Comfortable padding inside input */
  border: 1px solid #ccc; /* Standard border */
  border-radius: 4px;    /* Slightly rounded corners for inputs */
  box-sizing: border-box; /* Include padding and border in element's total width/height */
  font-size: 1rem; /* Standard font size */
  transition: border-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out; /* Smooth transitions */
}

input[type="text"]:focus,
input[type="email"]:focus {
  outline: none; /* Remove default browser outline */
  border-color: #007bff; /* Highlight border color on focus */
  box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.25); /* Add a subtle glow on focus */
}

/* Error Styling for Inputs */
input.has-error {
  border-color: #d33c40; /* Red border for error */
  background-color: #fff7f7; /* Slight red background tint */
}

input.has-error:focus {
   border-color: #d33c40; /* Keep border red on focus if error */
   box-shadow: 0 0 0 2px rgba(211, 60, 64, 0.25); /* Red glow on focus if error */
}

/* General Message Styling (applied to multiple message types) */
[class*='-message'], .error-text {
  font-weight: 500;
  margin-top: 0.5rem; /* Space above the message */
  font-size: 0.9em; /* Slightly smaller font for messages */
  padding: 0.5rem 0.75rem; /* Padding within message boxes */
  border-radius: 4px; /* Rounded corners for message boxes */
}

/* Specific Message Type Styling */
.error-message, .error-text {
  color: #d33c40; /* Red text */
  background-color: #fdecea; /* Light red background */
  border: 1px solid #f5c6cb; /* Light red border */
}

.error-text {
    padding: 0; /* Remove padding for inline field errors */
    background-color: transparent;
    border: none;
    margin-top: 0.3rem; /* Adjust spacing for inline errors */
}

.success-message {
  color: #155724; /* Dark green text */
  background-color: #d4edda; /* Light green background */
  border: 1px solid #c3e6cb; /* Light green border */
}

/* Button Styling */
button {
  display: block; /* Make button take full width */
  width: 100%;   /* Full width button */
  padding: 0.8rem 1.5rem; /* Generous padding */
  margin-top: 1rem; /* Space above the button */
  font-size: 1rem; /* Match input font size */
  font-weight: 600; /* Bolder text */
  color: #fff; /* White text */
  background-color: #007bff; /* Blue background */
  border: none; /* No border */
  border-radius: 4px; /* Rounded corners */
  cursor: pointer; /* Pointer cursor on hover */
  transition: background-color 0.2s ease-in-out, opacity 0.2s ease-in-out; /* Smooth transitions */
}

button:hover {
  background-color: #0056b3; /* Darker blue on hover */
}

button:focus {
    outline: none; /* Remove default outline */
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.5); /* Focus ring */
}

button:active {
  background-color: #004085; /* Even darker blue when clicked */
}

button:disabled {
  background-color: #6c757d; /* Grey background when disabled */
  cursor: not-allowed; /* Indicate non-interactive state */
  opacity: 0.7; /* Slightly faded */
}

/* Basic Mobile Responsiveness */
@media (max-width: 600px) {
  #employee-form {
    margin: 1rem; /* Reduce margin on smaller screens */
    padding: 1rem; /* Reduce padding */
  }

  /* Adjust font sizes slightly if needed on very small screens */
  /* label, input, button { font-size: 0.95rem; } */
}
</style>