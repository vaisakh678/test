Here�s how you can develop two REST APIs in **ASP.NET Core**:

1. **GET method**: Accepts an employee ID as a string and retrieves employee data.
2. **POST method**: Accepts a JSON body to log the working hours of an employee.

### Step-by-Step Guide

#### Step 1: Create an ASP.NET Core Web API Project

1. Open **Visual Studio**.
2. Go to **File** > **New** > **Project**.
3. Select **ASP.NET Core Web API** and click **Next**.
4. Name the project `EmployeeAPI`, select the location, and click **Create**.
5. Choose the **.NET 6** (or .NET 7 if available) framework and click **Create**.

#### Step 2: Create Models for Employee and LogHours

1. Right-click on the **Models** folder (create one if it doesn't exist).
2. Add two classes: `Employee.cs` and `LogHours.cs`.

**Employee.cs**

```csharp
namespace EmployeeAPI.Models
{
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Department { get; set; }
    }
}
```

**LogHours.cs**

```csharp
namespace EmployeeAPI.Models
{
    public class LogHours
    {
        public int Id { get; set; }
        public int EmpId { get; set; }
        public DateTime PunchIn { get; set; }
        public DateTime PunchOut { get; set; }
    }
}
```

#### Step 3: Create the Controller

1. Right-click on the **Controllers** folder.
2. Select **Add** > **Controller** > **API Controller - Empty**.
3. Name it `EmployeeController.cs`.

Inside the `EmployeeController.cs`, define the `GET` and `POST` methods.

```csharp
using EmployeeAPI.Models;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace EmployeeAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class EmployeeController : ControllerBase
    {
        private static List<Employee> Employees = new List<Employee>
        {
            new Employee { Id = 1, Name = "John Doe", Department = "Engineering" },
            new Employee { Id = 2, Name = "Jane Smith", Department = "HR" }
        };

        private static List<LogHours> LogHoursList = new List<LogHours>();

        // 1. GET Method to Retrieve Employee Data
        [HttpGet("{id}")]
        public ActionResult<Employee> GetEmployeeById(string id)
        {
            int empId;
            if (!int.TryParse(id, out empId))
            {
                return BadRequest("Invalid employee ID format.");
            }

            var employee = Employees.Find(e => e.Id == empId);

            if (employee == null)
            {
                return NotFound($"Employee with ID {id} not found.");
            }

            return Ok(employee);
        }

        // 2. POST Method to Log Working Hours
        [HttpPost("loghours")]
        public ActionResult LogHours([FromBody] LogHours logHours)
        {
            var employee = Employees.Find(e => e.Id == logHours.EmpId);
            if (employee == null)
            {
                return NotFound($"Employee with ID {logHours.EmpId} not found.");
            }

            LogHoursList.Add(logHours);
            return Ok("Working hours logged successfully.");
        }
    }
}
```

#### Explanation of the Code

1. **GET Method**:
   - This method accepts an employee ID as an input (`id` parameter) and retrieves the employee's data from the static list of employees.
   - If the employee ID is invalid or not found, it returns appropriate HTTP status codes (`BadRequest` or `NotFound`).
2. **POST Method**:
   - This method accepts a JSON body that contains employee working hours (`LogHours` object).
   - It checks if the employee exists, and if found, logs the working hours by adding them to the static `LogHoursList`.
   - If the employee doesn�t exist, it returns a `NotFound` error.

#### Step 4: Test the API

1. **GET Method Test**:

   - URL: `GET /api/employee/{id}`
   - Example: `GET /api/employee/1`
   - Response:
     ```json
     {
       "id": 1,
       "name": "John Doe",
       "department": "Engineering"
     }
     ```

2. **POST Method Test**:
   - URL: `POST /api/employee/loghours`
   - JSON Body Example:
     ```json
     {
       "id": 1,
       "empId": 1,
       "punchIn": "2024-09-06T08:00:00",
       "punchOut": "2024-09-06T16:00:00"
     }
     ```
   - Response:
     ```
     Working hours logged successfully.
     ```

#### Step 5: Run the Application

1. Press **F5** or click **Run** to launch the API.
2. You can use **Postman** or **cURL** to send requests to the API and verify the functionality.

### Conclusion

This guide demonstrates how to create a REST API in ASP.NET Core with a **GET** method to retrieve employee data and a **POST** method to log working hours for employees. The API uses simple in-memory data for demonstration purposes, but it can be extended to connect to a database for persistence.
