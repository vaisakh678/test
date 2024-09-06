Here's a step-by-step guide for creating an ASP.NET Core MVC web application using **Visual Studio** and adding the necessary packages for CRUD functionality.

### Step 1: Create a New ASP.NET Core Project in Visual Studio

1. **Open Visual Studio**:
   Launch Visual Studio and select `Create a new project` from the start screen.

2. **Select Project Template**:

   - In the search bar, type **ASP.NET Core Web App (Model-View-Controller)**.
   - Select the **ASP.NET Core Web App (MVC)** template.
   - Click **Next**.

3. **Configure Your Project**:

   - Project name: `EmployeeWebApp`
   - Location: Choose your preferred location.
   - Solution name: You can leave it as is.
   - Click **Next**.

4. **Configure the Target Framework**:
   - Select the latest **.NET Core** version.
   - Authentication: Select **None** (you can add authentication later if needed).
   - Uncheck **Enable Docker Support** and ensure **Configure for HTTPS** is checked.
   - Click **Create**.

Now, Visual Studio will create the basic structure of the MVC application for you.

---

### Step 2: Add Required NuGet Packages

1. **Open the NuGet Package Manager**:

   - Right-click on the **Solution** (or your project name) in the Solution Explorer.
   - Select **Manage NuGet Packages**.

2. **Install Entity Framework Core**:

   - In the **Browse** tab, search for the following packages and install them:
     - `Microsoft.EntityFrameworkCore`
     - `Microsoft.EntityFrameworkCore.SqlServer` (if you're using SQL Server) or `Microsoft.EntityFrameworkCore.Sqlite` (if you're using SQLite).
     - `Microsoft.EntityFrameworkCore.Tools` (for migrations).

3. **Install Bootstrap (Optional for Styling)**:
   - If you want to use Bootstrap for your UI styling, you can install the `Bootstrap` NuGet package by searching for it in the **NuGet Package Manager**.

---

### Step 3: Scaffold the MVC Views and Controller

1. **Create a Model**:

   - Right-click on the **Models** folder in Solution Explorer.
   - Select **Add** -> **Class**, and name it `Employee.cs`.
   - Define the Employee class like this:

     ```csharp
     public class Employee
     {
         public int EmployeeId { get; set; }
         public string FullName { get; set; }
         public string Status { get; set; } // E.g., "Active", "Inactive", "On Leave"
     }
     ```

2. **Create a DbContext**:

   - Right-click on the **Data** folder (or create one) and select **Add -> Class**.
   - Name it `AppDbContext.cs`, and configure it like this:

     ```csharp
     using Microsoft.EntityFrameworkCore;

     public class AppDbContext : DbContext
     {
         public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

         public DbSet<Employee> Employees { get; set; }
     }
     ```

3. **Update Startup.cs (or Program.cs)**:

   - Add the `AppDbContext` to your **dependency injection** in `Startup.cs` or `Program.cs`:

     ```csharp
     public void ConfigureServices(IServiceCollection services)
     {
         services.AddControllersWithViews();

         services.AddDbContext<AppDbContext>(options =>
             options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
     }
     ```

   - In **appsettings.json**, add the connection string:

     ```json
     "ConnectionStrings": {
         "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=EmployeeDb;Trusted_Connection=True;MultipleActiveResultSets=true"
     }
     ```

---

### Step 4: Scaffold CRUD Views and Controller

1. **Scaffold the Controller**:
   - Right-click on the **Controllers** folder in Solution Explorer.
   - Select **Add** -> **Controller**.
   - Choose **MVC Controller with views, using Entity Framework**.
   - Select the `Employee` model and `AppDbContext` for the DbContext.
   - Visual Studio will generate a controller and CRUD views automatically.

---

### Step 5: Add Employee Status Color Coding in View

1. Open the **Index.cshtml** file (Views -> Employees -> Index.cshtml).

2. Modify the table to include color coding based on employee status:

   ```html
   <tbody>
     @foreach (var item in Model) { var statusClass = item.Status == "Active" ?
     "text-success" : item.Status == "Inactive" ? "text-danger" :
     "text-warning";

     <tr>
       <td>@Html.DisplayFor(modelItem => item.EmployeeId)</td>
       <td>@Html.DisplayFor(modelItem => item.FullName)</td>
       <td class="@statusClass">@Html.DisplayFor(modelItem => item.Status)</td>
       <td>
         <a asp-action="Edit" asp-route-id="@item.EmployeeId">Edit</a> |
         <a asp-action="Details" asp-route-id="@item.EmployeeId">Details</a> |
         <a asp-action="Delete" asp-route-id="@item.EmployeeId">Delete</a>
       </td>
     </tr>
     }
   </tbody>
   ```

   This will color the employee status text as green for "Active", red for "Inactive", and yellow for other statuses.

---

### Step 6: Add Search Functionality

1. Add a **Search Form** in the **Index.cshtml** above the table:

   ```html
   <form method="get">
       <input type="text" name="searchString" value="@ViewData["SearchString"]" placeholder="Search Employees" />
       <input type="submit" value="Search" />
   </form>
   ```

2. Modify the **Index** action in the `EmployeeController.cs` to handle search queries:

   ```csharp
   public async Task<IActionResult> Index(string searchString)
   {
       var employees = from e in _context.Employees
                       select e;

       if (!String.IsNullOrEmpty(searchString))
       {
           employees = employees.Where(s => s.FullName.Contains(searchString) || s.Status.Contains(searchString));
       }

       return View(await employees.ToListAsync());
   }
   ```

---

### Step 7: Run the Application

1. Press **F5** or click on the **Run** button in Visual Studio.
2. Your web application should now run, allowing you to perform CRUD operations on employee data, view the status with color coding, and search by parameters.

---

Let me know if you need any additional clarification or adjustments for specific features!
