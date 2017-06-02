


<h2>**LINQ -- THE METHOD SYNTAX**</h2>

Choosing between these two approaches is purely a developer's choice. Performance wise both are the same. The query syntax is comparatively easy to understand but offers less flexibility. The method syntax, on the contrary, is a little difficult to understand but offers great flexibility. It is possible to chain multiple queries using the method syntax and thus achieve maximum results in a single statement.

 **1. Employee Result where lastname is  Davolio**

                using (NORTHWNDEntities1 db = new NORTHWNDEntities1())
                {
                      var employee = db.Employees.Where(p => p.LastName == "Davolio");
      Console.WriteLine(employee);
                    Console.ReadKey();
           }
              



  **2.Get List of Employees  where TitleofCourtesy is Mr.**

        using (NORTHWNDEntities1 db = new NORTHWNDEntities1())
        {
             IEnumerable<Employee> employee = db.Employees
                                                    .Where(p => p.TitleOfCourtesy == "Mr.");
            Console.ReadKey();
   }
     

 **TIP**
While using LINQ to Entities, it is important to understand when to use **IEnumerable** and **IQueryable.** If we use **IEnumerable**, the query will be executed immediately. If we use **IQueryable**, the query execution will be deferred until the application requests the enumeration.

Now let's see what should be considered while deciding whether to use **IQueryable** or **IEnumerable**. Using **IQueryable** gives you a chance to create a complex LINQ query using multiple statements without executing the query at the database level. The query gets executed only when the final LINQ query gets enumerated.              


----------
**3. GET ALL ROW OF EMPLOYEE TABLE**

    using (NORTHWNDEntities1 db = new NORTHWNDEntities1())
        {
              var employee = db.Employee;
            Console.ReadKey();
         }
         


----------
**4. Select Query**

            var employees = db.Employees.Where(p => p.LastName == "Davolio").Select(p => p.Employee1);
            Console.WriteLine(employees);
**5.List of Employees** 

     public class EmployeeModel
        {
            public int EmployeeId { get; set; }
            public string EmployeeName { get; set; }
            public string TitleOfCourtesy { get; set; }
        }

    using (NORTHWNDEntities1 db = new NORTHWNDEntities1())
                {
                    IQueryable<EmployeeModel> employee = db.Employees.Select
                        (p => new EmployeeModel
                        {
                            EmployeeId = p.EmployeeID,
                            EmployeeName = p.FirstName +p.LastName,
                            TitleOfCourtesy = p.TitleOfCourtesy
    
                        });
                    Console.WriteLine(employee);
                    Console.ReadKey();
                }


The preceding code is essentially doing the same thing as in the previous query but uses the method syntax instead. Using projections with LINQ to Entities is very useful because it enables us to return complex types that contain data from multiple entities. Entity Framework will take care of creating the appropriate SQL query accordingly.
**6.Grouping using LINQ to Entities**

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
       var result = db.Employees.GroupBy
                    (employee => employee.Employer_ID)
                    .Select
                    (
                          employeeGroup =>
                          new
                          {
                                EmployerID = employeeGroup.Key,
                               EmployeeEntity = employeeGroup
                          }
                    );
    }

The preceding code will group the Employee records by Employer_ID. The results will be returned as a projection that will create an anonymous object. The Employer_ID property of the result will contain the ID of the employer with which we have performed grouping. The EmployeeEntity property will contain a collection of Employees found for this EmployerID.


----------


**6.ORDER BY **

    using (NORTHWNDEntities1 db = new NORTHWNDEntities1())
                {
                    var result = db.Employees.OrderBy(p => p.EmployeeID)
                    Console.ReadKey();
                }


----------

**7.Order By Desc**

    using (NORTHWNDEntities1 db = new NORTHWNDEntities1())
                {
                    var result = db.Employees.OrderByDescending(p => p.EmployeeID)
                    Console.ReadKey();
                }
                


----------

**Aggregate operators with LINQ to Entities**
Let's now take a look at how we can use aggregate operators using LINQ to Entities. We will see how we can perform the following aggregate operations using LINQ to Entities:

 1. Count 
 2. Sum 
 3. Min 
 4. Max 
 5. Average
 
**1. Count**

     using (NORTHWNDEntities1 db = new NORTHWNDEntities1())
                {
                    var result = db.Employees.Where(p => p.TitleOfCourtesy == "Mr.").Count();
                    Console.WriteLine(result);
                    Console.ReadKey();
                }
**2.Sum**
 
  

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
       int sumOfSalaries = db.Employees
                        .Where(employee => employee.Employer_ID == 4)
                        .Select(employee => employee.Salary)
                        .Sum();
    }
**3. Min**

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
       int minimumSalary = db.Employees
                    .Where(employee => employee.Employer_ID == 4)
                    .Select(employee => employee.Salary)
                    .Min();
    }
**4.MAX**

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
       int maximumSalary = db.Employees
             .Where(employee => employee.Employer_ID == 4)
             .Select(employee => employee.Salary)
             .Max();
    }

**5. Average**

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
        double averageSalary = db.Employees
                    .Where(employee => employee.Employer_ID == 4)
                    .Select(employee => employee.Salary)
                    .Average();
    }


----------
**Partitioning/paging data using LINQ to Entities**

 1. Skip 

**1.Skip**

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
       var employees = db.Employees
             .OrderBy(employee => employee.ID)
             .Skip(10);
    }

**2.Take**

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
       var employees = db.Employees
                    .OrderBy(employee => employee.ID)
                    .Take(10);
    }


----------
**IMPLEMENTING PAGING**
	If we want to implement paging, we have to use Skip and Take in the same query. For example, if we want to retrieve the second page of the data, and each page shows 10 records, we have to skip the first 10 records and then fetch the next 10 records for the user:

    using (SampleDatabaseEntities db = new SampleDatabaseEntities())
    {
       var employees = db.Employees
             .OrderBy(employee => employee.ID)
             .Skip(10)
             .Take(10);
    }


----------
**Implementing join using LINQ to Entities**

using (SampleDatabaseEntities db = new SampleDatabaseEntities())
{
   var employersList = db.Employers.GroupJoin
         (
                db.Employees,
                employer => (Int32?)(employer.ID),
                employee => employee.Employer_ID,
                (employer, employeeGroup) => new
                {
                      EmployerName = employer.EmployerName,
                      EmployeesList = employeeGroup
                }
          );
}

           The join or GroupJoin is the LINQ equivalent of SQL LEFT OUTER JOIN. This will always return all the elements of the entity collection that are to the left irrespective of whether or not the entity collection on the right-hand side of join contains any entities.     


----------
**In the End we dicuss lazy loading and eager loading**




                 
  





