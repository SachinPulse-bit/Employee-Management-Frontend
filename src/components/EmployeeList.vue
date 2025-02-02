<template>
  <div>
    <h1>Employee List</h1>

    <!-- Filters -->
    <div class="filters">
      <input type="text" v-model="searchName" placeholder="Search by name" />
      <select v-model="searchDepartment">
        <option value="">All Departments</option>
        <option v-for="dept in departments" :key="dept" :value="dept">{{ dept }}</option>
      </select>
      <button class="create-btn" @click="fetchEmployees">Search</button>
      <button class="create-btn" @click="openForm(null)">Create Employee</button>
    </div>

    <!-- Employee Table -->
    <table border="1" class="employee-table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Name</th>
          <th>Department</th>
          <th>Experience</th>
          <th>Salary</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="employee in employees" :key="employee.id">
          <td>{{ employee.id }}</td>
          <td>{{ employee.name }}</td>
          <td>{{ employee.department }}</td>
          <td>{{ employee.experience }}</td>
          <td>{{ employee.salary }}</td>
          <td>
            <button class="update-btn" @click="openForm(employee)">Update</button>
            <button class="delete-btn" @click="deleteEmployee(employee.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Pagination Controls -->
    <div class="pagination">
      <button @click="prevPage" :disabled="page === 0">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="page >= totalPages - 1">Next</button>
    </div>

    <!-- Employee Form Modal -->
    <div v-if="showForm" class="modal-overlay">
      <EmployeeForm :employeeData="selectedEmployee" @close="showForm = false" @refresh="fetchEmployees" />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import EmployeeForm from "./EmployeeForm.vue";

export default {
  name: "EmployeeList",
  components: { EmployeeForm },
  data() {
    return {
      employees: [],
      searchName: "",
      searchDepartment: "",
      departments: ["CSO","DevOps", "HR", "IT Support", "Prduct Management", "QA & Testing", 
        "Sales & Marketing", "Software Development", "UI/UX Design"],
      showForm: false,
      page: 0,
      size: 5,
      totalPages: 1,
      currentPage: 1, // Track current page for display
      totalRecords: 0, // Track total records for display
    };
  },
  watch: {
    showForm(newVal) {
      document.body.classList.toggle("modal-open", newVal);
    }
  },
  created() {
    this.fetchEmployees(); // Fetch employees on page load
  },
  methods: {
    // Method to fetch employees with search and pagination
    async fetchEmployees() {
      try {
        const response = await axios.get("http://localhost:8080/api/employees/search", {
          params: {
            name: this.searchName || null,
            department: this.searchDepartment || null,
            page: this.page,
            size: this.size,
          },
        });

        // Ensure response data matches the structure
        this.employees = response.data.data || [];
        this.totalPages = response.data.totalPages || 1;
        this.currentPage = response.data.currentPage || this.page + 1; // Adjust for 0-based index
        this.totalRecords = response.data.totalRecords || 0;
        console.error("Error this.totalPages:", this.totalPages);
        console.error("Error this.currentPage:", this.currentPage);
        console.error("Error this.totalRecords:", this.totalRecords);
      } catch (error) {
        console.error("Error fetching employees:", error);
      }
    },

    nextPage() {
      if (this.page < this.totalPages - 1) {
        this.page++;
        this.fetchEmployees();
      }
    },

    prevPage() {
      if (this.page > 0) {
        this.page--;
        this.fetchEmployees();
      }
    },

    // Method to delete an employee
    async deleteEmployee(employeeId) {
      try {
        await axios.delete(`http://localhost:8080/api/employees/delete/${employeeId}`);
        this.fetchEmployees(); // Refresh after deletion
      } catch (error) {
        console.error("Error deleting employee:", error);
      }
    },

    // Open the form to create or edit an employee
    openForm(employee) {
      this.selectedEmployee = employee ? { ...employee } : null;
      this.showForm = true;
    }
  },
};
</script>

<style scoped>
/* General Styles */
body {
  font-family: 'Arial', sans-serif;
  background-color: #f4f4f9;
  margin: 0;
  padding: 0;
  color: #333;
}

h1 {
  text-align: center;
  color: #007bff;
  margin: 20px 0;
}

/* Table Styles */
.employee-table {
  width: 90%;
  margin: 20px auto;
  border-collapse: collapse;
  background: #fff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
}

.employee-table th,
.employee-table td {
  padding: 12px 15px;
  text-align: center;
  border-bottom: 1px solid #ddd;
}

.employee-table th {
  background-color: #007bff;
  color: #fff;
  text-transform: uppercase;
  font-size: 14px;
}

.employee-table tr:nth-child(even) {
  background-color: #f9f9f9;
}

.employee-table tr:hover {
  background-color: #f1f1f1;
}

/* Button Styles */
button {
  padding: 8px 15px;
  margin: 5px;
  border: none;
  cursor: pointer;
  font-size: 14px;
  border-radius: 5px;
  transition: all 0.3s ease;
}

.create-btn {
  background-color: #007bff;
  color: #fff;
}

.update-btn {
  background-color: #28a745;
  color: #fff;
}

.delete-btn {
  background-color: #dc3545;
  color: #fff;
}

button:hover {
  opacity: 0.9;
}

button:disabled {
  background-color: #ccc !important;
  color: #999 !important;
  cursor: not-allowed !important;
}

/* Filters */
.filters {
  display: flex;
  justify-content: center;
  margin: 20px auto;
  gap: 10px;
}

.filters input,
.filters select {
  width: 15%;
  padding: 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px; /* Match the label font size */
  font-family: Arial, sans-serif;
}

.filters input:focus,
.filters select:focus {
  border-color: #007bff;
  outline: none;
}

/* Pagination */
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin: 20px 0;
}

.pagination button {
  padding: 10px 15px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.pagination span {
  font-size: 14px;
  color: #555;
}

/* Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-header {
  text-align: center;
  font-size: 18px;
  margin-bottom: 10px;
}

.modal-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.modal-buttons button {
  flex: 1;
  margin: 0 5px;
}
</style>
