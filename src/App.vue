<template>
    <div class="small-container">
        <h1>Employees</h1>
        <employee-form @add:employee="addEmployee"  />
        <employee-table 
        :employees="employees"  
        @delete:employee="deleteEmployee" 
        @edit:employee="editEmployee"
         />
    </div>
</template>

<script>
import EmployeeForm from './components/EmployeeForm.vue'
import EmployeeTable from './components/EmployeeTable.vue'

export default {
  name: 'App',
  components: {
   EmployeeTable,
   EmployeeForm
  },
  data(){
       
    return {
            employees:[]
        }
  },
  methods:{
    async addEmployee(employee){

        try {
            const  response = await fetch('https://jsonplaceholder.typicode.com/users',
                {
                    method:'POST',
                    body:JSON.stringify(employee),
                    headers:{ 'Content-type': 'application/json; charset=UTF-8'}
                }
            );

            const data = await response.json();

            this.employees = [...this.employees,data];
            
        } catch (error) {
            console.log(error);
        }
        
    },

    async deleteEmployee(id){
        try {
            await fetch('https://jsonplaceholder.typicode.com/users/'+id,{
                method:'DELETE'
            })

            this.employees = this.employees.filter((employee)=>{
                    return employee.id != id;
            })
            
        } catch (error) {
             console.log(error);
        }
    },
    async editEmployee(id,updatedEmployee){
        try {
            const response = await fetch('https://jsonplaceholder.typicode.com/users/'+id,
                {
                    method:'PUT',
                    body:JSON.stringify(updatedEmployee),
                    headers:{ 'Content-type': 'application/json; charset=UTF-8'}
                }
            );

            const data = await response.json();

            this.employees.map((employee)=>{
                return employee.id==id?data:employee;
            })
        } catch (error) {
            console.log(error);
        }
    },
    async getEmployees(){
        try {
            const response = await fetch('https://jsonplaceholder.typicode.com/users',{method:'GET'})
            const data = await response.json();
            this.employees = data;

        } catch (error) {
            console.log(error);
        }
    }
  },
  mounted(){
        this.getEmployees();
  }
}
</script>

<style>
body {
  font-family: sans-serif;
  margin: 20px;
  background-color: #f4f4f4;
  color: #333;
}

.small-container {
  max-width: 90%; /* Adjust for larger mobile screens and tablets */
  margin: 20px auto; /* Center the container */
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

h1 {
  color: #007bff; /* A more modern primary color */
  text-align: center;
  margin-bottom: 20px;
}

/* Style for the Employee Form component */
.employee-form {
  margin-bottom: 20px;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background-color: #f9f9f9;
}

.form-group {
  margin-bottom: 10px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #555;
}

input[type="text"],
input[type="email"] {
  width: calc(100% - 12px); /* Adjust for padding */
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box; /* Prevent padding from increasing width */
  font-size: 1rem;
}

.add-button {
  background-color: #28a745; /* Success color */
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease;
}

.add-button:hover {
  background-color: #1e7e34;
}

/* Style for the Employee Table component */
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
  border-radius: 6px;
  background-color: #fff;
}

thead {
  background-color: #f8f8f8;
}

th {
  padding: 12px;
  text-align: left;
  border-bottom: 2px solid #ccc;
  color: #333;
}

td {
  padding: 12px;
  border-bottom: 1px solid #eee;
  color: #555;
}

tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

/* Style for input fields within the table (edit mode) */
#employee-table input[type="text"] {
  width: 100%;
  padding: 8px;
  margin: 0;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 1rem;
}

/* Style for buttons within the table */
#employee-table button {
  background-color: #007bff; /* Primary color */
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9rem;
  margin-right: 5px;
  transition: background-color 0.3s ease;
}

#employee-table button:hover {
  background-color: #0056b3;
}

#employee-table .muted-button {
  background-color: #6c757d; /* Secondary/muted color */
}

#employee-table .muted-button:hover {
  background-color: #545b62;
}

/* Mobile-specific styles */
@media (max-width: 600px) {
  .small-container {
    padding: 15px;
    margin: 15px auto;
  }

  h1 {
    font-size: 2rem;
    margin-bottom: 15px;
  }

  .employee-form {
    padding: 10px;
    margin-bottom: 15px;
  }

  label {
    font-size: 0.9rem;
  }

  input[type="text"],
  input[type="email"] {
    font-size: 0.9rem;
    padding: 6px;
  }

  .add-button {
    font-size: 0.9rem;
    padding: 8px 12px;
  }

  table {
    border: none;
  }

  thead {
    display: none;
  }

  tbody tr {
    display: block;
    margin-bottom: 15px;
    border: 1px solid #ddd;
    border-radius: 6px;
    padding: 15px;
    background-color: #fff;
  }

  tbody td {
    display: flex;
    text-align: left;
    padding-left: 50%;
    position: relative;
    border-bottom: none;
    padding-bottom: 8px;
  }

  tbody td:before {
    content: attr(data-label);
    position: absolute;
    left: 10px;
    padding-left: 0;
    font-weight: bold;
    text-transform: uppercase;
    font-size: 0.7rem;
    color: #777;
    top: 8px;
  }

  #employee-table input[type="text"] {
    font-size: 0.9rem;
    padding: 6px;
  }

  #employee-table button {
    font-size: 0.8rem;
    padding: 6px 10px;
    margin-right: 3px;
  }

  tbody td:last-child {
    display: flex;
    gap: 5px;
    padding-left: 10px; /* Reset padding */
  }

  tbody td:last-child button {
    flex-grow: 1; /* Distribute space evenly */
    margin-right: 0;
  }
}
</style>