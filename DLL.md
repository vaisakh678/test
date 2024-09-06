Here's a step-by-step guide to creating a solution with Visual Studio that incorporates two tables (`Employee` and `LogHours`) in a **Database Access Layer (DBAL)** and a **Service Layer**. We'll walk through everything from creating the project to testing the solution.

### Step 1: **Create a New Solution in Visual Studio**

1. Open **Visual Studio**.
2. Go to **File** > **New** > **Project**.
3. Choose **Class Library (.NET Core)** or **Class Library (.NET Framework)** depending on your target framework.
   - Name the first project **DBAL** (for Database Access Layer).
   - Choose a location and click **Create**.
4. Repeat the process to create another **Class Library** named **Service** (for Business Logic).
5. Finally, create a **Console Application** to test the solution and name it **ConsoleApp**.
6. After this, you should have three projects: `DBAL`, `Service`, and `ConsoleApp`.

### Step 2: **Add Project References**

1. Right-click on the **ConsoleApp** project in **Solution Explorer**.
2. Select **Add** > **Project Reference**.
3. Check both the **DBAL** and **Service** projects and click **OK**.
4. Similarly, add a reference to the **DBAL** project inside the **Service** project, since the Service Layer depends on DBAL.

### Step 3: **Install Required NuGet Packages**

1. Right-click on the **DBAL** project.
2. Select **Manage NuGet Packages**.
3. In the **Browse** tab, search for and install:
   - `Dapper` (for data access).
   - `System.Data.SqlClient` (for .NET Framework) or `Microsoft.Data.SqlClient` (for .NET Core).

### Step 4: **Create the Database Access Layer (DBAL)**

The **DBAL** will be responsible for connecting to the database and executing SQL queries or stored procedures.

#### 4.1 Define the DBAL Interface

1. Right-click on the **DBAL** project and choose **Add** > **New Item** > **Interface**.
2. Name it `IDatabase.cs`.
3. Add the following code to the interface:

```csharp
using System.Data;

namespace DBAL
{
    public interface IDatabase
    {
        IDbConnection CreateConnection();
        T ExecuteStoredProcedure<T>(string storedProcName, object parameters);
    }
}
```

#### 4.2 Implement the DBAL Class

1. Right-click on the **DBAL** project and choose **Add** > **New Item** > **Class**.
2. Name it `Database.cs`.
3. Add the following code:

```csharp
using System.Data;
using System.Data.SqlClient;
using Dapper;

namespace DBAL
{
    public class Database : IDatabase
    {
        private readonly string _connectionString;

        public Database(string connectionString)
        {
            _connectionString = connectionString;
        }

        public IDbConnection CreateConnection()
        {
            return new SqlConnection(_connectionString);
        }

        public T ExecuteStoredProcedure<T>(string storedProcName, object parameters)
        {
            using (var connection = CreateConnection())
            {
                connection.Open();
                return connection.QuerySingleOrDefault<T>(storedProcName, parameters, commandType: CommandType.StoredProcedure);
            }
        }
    }
}
```

### Step 5: **Create the Service Layer**

The **Service Layer** will handle business logic and work with the DBAL to retrieve or process data.

#### 5.1 Create the Models

Right-click the **Service** project and add a folder called `Models`. Inside this folder, define the models for `Employee` and `LogHours`.

1. **Employee Model**:

   ```csharp
   namespace Service.Models
   {
       public class Employee
       {
           public int Id { get; set; }
           public string Name { get; set; }
           public string Department { get; set; }
       }
   }
   ```

2. **LogHours Model**:
   ```csharp
   namespace Service.Models
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

#### 5.2 Define the Service Interface

1. Right-click the **Service** project and choose **Add** > **New Item** > **Interface**.
2. Name it `IEmployeeService.cs`.
3. Add the following code to the interface:

```csharp
using Service.Models;

namespace Service
{
    public interface IEmployeeService
    {
        Employee GetEmployeeById(int employeeId);
        IEnumerable<LogHours> GetLogHoursByEmployeeId(int employeeId);
    }
}
```

#### 5.3 Implement the Service Class

1. Right-click the **Service** project and choose **Add** > **New Item** > **Class**.
2. Name it `EmployeeService.cs`.
3. Add the following code:

```csharp
using DBAL;
using Service.Models;
using System.Collections.Generic;

namespace Service
{
    public class EmployeeService : IEmployeeService
    {
        private readonly IDatabase _database;

        public EmployeeService(IDatabase database)
        {
            _database = database;
        }

        public Employee GetEmployeeById(int employeeId)
        {
            var parameters = new { EmployeeId = employeeId };
            return _database.ExecuteStoredProcedure<Employee>("GetEmployeeById", parameters);
        }

        public IEnumerable<LogHours> GetLogHoursByEmployeeId(int employeeId)
        {
            var parameters = new { EmployeeId = employeeId };
            return _database.ExecuteStoredProcedure<IEnumerable<LogHours>>("GetLogHoursByEmployeeId", parameters);
        }
    }
}


### Step 8: **Build and Run the Application**

1. Right-click on the **Solution** in **Solution Explorer** and select **Build Solution**.
2. After the build completes, right-click the **ConsoleApp** project and choose **Set as Startup Project**.
3. Press **Ctrl+F5** or click the **Run** button to execute the program.

When prompted, enter an employee ID, and the console application will fetch and display the employee's details along with their log hours.

### Conclusion

You've now created a **DBAL** and **Service Layer** in .NET using Visual Studio's UI, connected it to a database, and tested the solution with a **Console Application**. This architecture promotes good separation of concerns between database access and business logic, and it can be easily extended with additional features or services.
```
