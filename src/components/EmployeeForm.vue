<template>
  <div class="form-container">
    <h2>{{ employeeData ? "Edit Employee" : "Create Employee" }}</h2>
    <form @submit.prevent="saveEmployee">
      <label for="name">Name:</label>
      <input type="text" id="name" v-model="employee.name" required />

      <label for="salary">Salary:</label>
      <input type="number" id="salary" v-model="employee.salary" required />

      <label for="department">Department:</label>
      <select id="department" v-model="employee.department" required>
        <option disabled value="">Select Department</option>
        <option v-for="dept in departments" :key="dept" :value="dept">{{ dept }}</option>
      </select>

      <label for="experience">Experience:</label>
      <input type="number" id="experience" v-model="employee.experience" required />

      <button type="submit">{{ employeeData ? "Update Employee" : "Create Employee" }}</button>
      <button type="button" @click="$emit('close')">Cancel</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "EmployeeForm",
  props: ["employeeData"],
  data() {
    return {
      employee: {
        id: null,
        name: "",
        salary: "",
        department: "",
        experience: ""
      },
      departments: ["CSO","DevOps", "HR", "IT Support", "Prduct Management", "QA & Testing", 
      "Sales & Marketing", "Software Development", "UI/UX Design"],
    };
  },
  created() {
    if (this.employeeData) {
      this.employee = { ...this.employeeData }; // Clone existing data for editing
    }
  },
  methods: {
    async saveEmployee() {
      try {
        if (this.employeeData) {
          await axios.put(`http://localhost:8080/api/employees/update/${this.employee.id}`, this.employee);
        } else {
          await axios.post("http://localhost:8080/api/employees/save", this.employee);
        }
        this.$emit("refresh"); // Refresh list in EmployeeList.vue
        this.$emit("close"); // Close modal
      } catch (error) {
        console.error("Error saving employee:", error);
      }
    }
  }
};
</script>

<style scoped>
/* General Heading */
h2 {
  text-align: center;
  color: #007bff;
  margin: 20px 0;
  font-size: 24px; /* Larger font size for the heading */
}

/* Form Container */
.form-container {
  background: white; 
  padding: 20px;
  border-radius: 10px;
  width: 400px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

/* Label Styles */
label {
  display: block;
  margin-top: 10px;
  font-size: 16px; /* Enhanced font size for better readability */
  color: #333; /* Slightly darker color for contrast */
  font-weight: 600; /* Bold for emphasis */
}

/* Input Styles */
input{
  width: 95%;
  padding: 10px;
  margin-top: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
  font-size: 16px; /* Match the label font size */
  font-family: Arial, sans-serif;
}

select {
  width: 100%;
  padding: 10px;
  margin-top: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
  font-size: 16px; /* Match the label font size */
  font-family: Arial, sans-serif;
}

/* Button Styles */
button {
  margin-top: 15px;
  width: 100%;
  padding: 12px; /* More padding for a larger clickable area */
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 18px; /* Slightly larger font for buttons */
  font-weight: bold;
  text-transform: uppercase; /* For a stronger visual */
}

/* Different Button Colors */
button[type="submit"] {
  background: green;
  color: white;
  transition: background 0.3s ease;
}

button[type="button"] {
  background: red;
  color: white;
  transition: background 0.3s ease;
}

button:hover {
  opacity: 0.9; /* Subtle feedback on hover */
}

/* Focus Styles */
input:focus, select:focus {
  border-color: #007bff; /* Blue focus border */
  outline: none; /* Remove default outline */
}

/* Add Responsiveness */
@media (max-width: 500px) {
  .form-container {
    width: 90%; /* Adjust width for smaller screens */
    padding: 15px;
  }

  label {
    font-size: 14px; /* Slightly smaller labels on small screens */
  }

  input, select {
    font-size: 14px; /* Match label size */
  }

  button {
    font-size: 16px; /* Slightly smaller buttons for smaller screens */
  }
}

</style>
