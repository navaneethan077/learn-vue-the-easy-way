
<template>
    <div id="employee-table">
        <p v-if="employees.length<1" class="empty-table">No employees</p>
        <table v-else>
            <thead>
                <tr>
                <th>Employee name</th>
                <th>Employee email</th>
                <th>Action</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="employee in employees" :key="employee.id">
                    <td v-if="editing==employee.id">
                        <input type="text" v-model="employee.name">
                    </td>
                    <td v-else>{{employee.name}}</td>
                    <td v-if="editing==employee.id">
                        <input type="text" v-model="employee.email">
                    </td>
                    <td v-else>{{employee.email}}</td>
                    <td v-if="editing==employee.id">
                        <button @click="editEmployee(employee)">Save</button>
                        <button class="muted-button" @click="cancelEdit(employee)">Cancel</button>
                    </td>
                    <td v-else>
                        <button @click="editMode(employee)" >Edit</button>
                        <button @click="$emit('delete:employee',employee.id)">Delete</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
<script>
export default {
    name : 'employee-table',
    props:{
        employees:Array
    },
    data(){
        return {
            editing:null,
            employee:{
                name:'',
                email:''
            },
            cachedEmployee:null
        }
    },
    methods:{
        editMode(employee){
            this.editing = employee.id;
            this.cachedEmployee = Object.assign({},employee);
        },
        editEmployee(employee){
            if (employee.name==''||employee.email=='') {
                return;
            }
            this.$emit('edit:employee',employee.id,employee);
            this.editing=null;
        },
        cancelEdit(employee){
            this.editing=null
            Object.assign(employee,this.cachedEmployee);
        }
    }
}
</script>
<style scoped>
#employee-table {
  width: 100%;
  overflow-x: auto; /* Enable horizontal scrolling for smaller screens */
}

.empty-table {
  padding: 1rem;
  text-align: center;
  color: #777;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 1rem;
  border: 1px solid #ddd;
}

thead {
  background-color: #f8f8f8;
}

th {
  padding: 0.75rem;
  text-align: left;
  border-bottom: 2px solid #ccc;
}

td {
  padding: 0.75rem;
  border-bottom: 1px solid #eee;
}

input[type="text"] {
  width: 100%;
  padding: 0.5rem;
  margin-bottom: 0;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box; /* Ensure padding doesn't affect width */
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 0.5rem 0.75rem;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: background-color 0.3s ease;
  margin-right: 0.5rem;
}

button:hover {
  background-color: #0056b3;
}

.muted-button {
  background-color: #6c757d;
}

.muted-button:hover {
  background-color: #545b62;
}

/* Mobile-specific styles */
@media (max-width: 600px) {
  table {
    border: none; /* Remove border on smaller screens for better visual */
  }

  thead {
    display: none; /* Hide table headers on mobile */
  }

  tbody tr {
    display: block;
    margin-bottom: 1rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    padding: 0.75rem;
  }

  tbody td {
    display: flex;
    text-align: left;
    padding-left: 50%; /* Leave space for label */
    position: relative;
    border-bottom: none;
  }

  tbody td:before {
    content: attr(data-label);
    position: absolute;
    left: 0;
    padding-left: 0.75rem;
    font-weight: bold;
    text-transform: uppercase;
    font-size: 0.8rem;
    color: #777;
  }

  /* Style for input fields on mobile */
  tbody td input[type="text"] {
    width: calc(100% - 0.75rem); /* Adjust input width */
  }

  /* Adjust button layout on mobile */
  tbody td:last-child {
    display: flex;
    gap: 0.5rem;
    padding-left: 0.75rem; /* Reset padding */
  }

  tbody td:last-child button {
    width: 50%; /* Make buttons take up half the width */
    margin-right: 0; /* Remove right margin */
  }
}
</style>
