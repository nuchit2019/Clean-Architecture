## Clean Architecture คืออะไร?  
**Clean Architecture** เป็นหลักการออกแบบซอฟต์แวร์ที่มุ่งเน้นให้ซอฟต์แวร์มีโครงสร้างที่ดี แยกส่วนประกอบต่าง ๆ ออกจากกัน ทำให้ **ยืดหยุ่น, ทดสอบง่าย, และขยายได้ในระยะยาว**  

 <p><img width="100%" src="/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg" /></p>

#

### 🔹 **หลักการสำคัญของ Clean Architecture**
1. **แยก Concerns ตามเลเยอร์ (Separation of Concerns)**  
   - แต่ละชั้นมีหน้าที่ของตัวเอง ไม่ยุ่งเกี่ยวกับรายละเอียดของชั้นอื่น
2. **พึ่งพา Abstractions แทน Implementations**  
   - ลดการพึ่งพาสิ่งที่เปลี่ยนแปลงบ่อย เช่น UI, Database, External Services
3. **ทำให้ระบบสามารถทดสอบได้ง่าย (Testability)**  
   - โดยใช้ Dependency Injection (DI) และ Interface
4. **ลดการ Coupling**  
   - ทำให้แต่ละส่วนของระบบเป็นอิสระจากกันมากที่สุด
5. **เพิ่ม Reusability**  
   - สามารถใช้ Business Logic ซ้ำได้โดยไม่ต้องผูกกับ Framework หรือ Library เฉพาะ

#

### 🔹 **โครงสร้างของ Clean Architecture**
Clean Architecture จะแบ่งโค้ดออกเป็น **4 ชั้นหลัก** ตามหลัก **Dependency Rule** (การพึ่งพาจากภายนอกเข้ามาข้างในเท่านั้น)

#### 🏛️ **1. Entities (Domain Layer)**
   - เป็น **Core Business Rules** หรือ กฎของระบบ
   - ไม่มีการพึ่งพา Framework, UI, หรือ Database ใด ๆ
   - มักใช้ **POCO (Plain Old CLR Object)** หรือ **Enterprise-wide Business Rules**

#### 📜 **2. Use Cases (Application Layer)**
   - กำหนด **Business Logic** และ **Workflow ของแอปพลิเคชัน**
   - ควบคุมลำดับการทำงานของระบบ เช่น การคำนวณ, การ Validate, การเรียก Data จาก Repository
   - มักใช้ **Service, Interactors หรือ Application Services**
   - **ไม่ควร** มีโค้ดที่พึ่งพา Database, UI หรือ External APIs โดยตรง

#### 📦 **3. Interface Adapters (Infrastructure Layer)**
   - ทำหน้าที่แปลงข้อมูลให้เหมาะสมกับ **Use Cases และ Entities**
   - เช่น **Controllers, Presenters, ViewModels, Repository, DTOs**
   - สามารถใช้ **Dapper, Entity Framework, API Gateway หรือ Messaging Queue** ในชั้นนี้

#### 🌍 **4. Frameworks & Drivers (External Layer)**
   - ชั้นนี้เป็น **Detail Implementation** ของระบบ เช่น:
     - **Database** (SQL, NoSQL, Oracle)
     - **UI / API (ASP.NET, Blazor, Angular, React)**
     - **External Services (Firebase, Azure Storage, AWS S3)**
     - **ORM (Dapper, EF Core)**
   - เป็นชั้นที่สามารถถูกแทนที่หรือเปลี่ยนแปลงได้ง่าย

#

### 🔹 **Dependency Rule (กฎสำคัญของ Clean Architecture)**
> **"Source code dependencies can only point inwards."**  
> (การพึ่งพาของโค้ดต้องชี้จากภายนอกเข้ามาด้านในเท่านั้น)

🔄 หมายความว่า:
- UI **พึ่งพา** Use Cases
- Use Cases **พึ่งพา** Entities
- แต่ **Entities ไม่พึ่งพาอะไรเลย!**  
- Database, Frameworks และ External APIs **ไม่ควรส่งผลกระทบโดยตรงต่อ Business Logic**

#

### 🔹 **ตัวอย่าง Clean Architecture ใน C# WebAPI**
```csharp
// ✅ Domain Layer (Entities)
public class Receipt
{
    public int Id { get; set; }
    public decimal Amount { get; set; }
    public DateTime Date { get; set; }
}

// ✅ Application Layer (Use Case)
public interface IGetReceiptsUseCase
{
    Task<IEnumerable<Receipt>> ExecuteAsync();
}

public class GetReceiptsUseCase : IGetReceiptsUseCase
{
    private readonly IReceiptRepository _repository;

    public GetReceiptsUseCase(IReceiptRepository repository)
    {
        _repository = repository;
    }

    public async Task<IEnumerable<Receipt>> ExecuteAsync()
    {
        return await _repository.GetAllReceiptsAsync();
    }
}

// ✅ Infrastructure Layer (Repository)
public class ReceiptRepository : IReceiptRepository
{
    private readonly IDbConnection _dbConnection;

    public ReceiptRepository(IDbConnection dbConnection)
    {
        _dbConnection = dbConnection;
    }

    public async Task<IEnumerable<Receipt>> GetAllReceiptsAsync()
    {
        var sql = "SELECT * FROM Receipts";
        return await _dbConnection.QueryAsync<Receipt>(sql);
    }
}

// ✅ Presentation Layer (Controller)
[ApiController]
[Route("api/receipts")]
public class ReceiptsController : ControllerBase
{
    private readonly IGetReceiptsUseCase _getReceiptsUseCase;

    public ReceiptsController(IGetReceiptsUseCase getReceiptsUseCase)
    {
        _getReceiptsUseCase = getReceiptsUseCase;
    }

    [HttpGet]
    public async Task<IActionResult> Get()
    {
        var receipts = await _getReceiptsUseCase.ExecuteAsync();
        return Ok(receipts);
    }
}
```
#

### 🔹 **ข้อดีของ Clean Architecture**
✅ **แยก Business Logic ออกจาก UI และ Database**  
✅ **ทดสอบง่าย (Testability สูง)** เพราะสามารถใช้ Unit Test กับ Use Cases ได้โดยไม่ต้องยุ่งกับ Database  
✅ **เปลี่ยน Database หรือ UI ได้ง่าย** โดยไม่กระทบ Business Logic  
✅ **ลดการ Coupling ระหว่าง Components**  
✅ **โค้ดสะอาด อ่านง่าย และดูแลรักษาง่าย**

#

### 🔹 **ข้อเสียของ Clean Architecture**
❌ **อาจดูซับซ้อนเกินไปสำหรับโปรเจ็กต์เล็ก ๆ**  
❌ **ต้องใช้ Dependency Injection และ Interface เยอะ** ทำให้ต้องมีความรู้เรื่อง Design Patterns  
❌ **Performance อาจลดลงนิดหน่อย** เพราะมีหลาย Layer  

#

### 🔹 **Clean Architecture เหมาะกับอะไร?**
✅ **ระบบที่มี Business Logic ซับซ้อน**  
✅ **Microservices & Web APIs** (เหมาะกับ Solution Tni.Gis.Receipts ที่ใช้ C# WebAPI)  
✅ **ระบบที่ต้องการ Testability สูง**  
✅ **ระบบที่ต้องเปลี่ยน UI หรือ Database ได้โดยง่าย**  

แต่ถ้าระบบเป็นแค่ CRUD ธรรมดา Clean Architecture อาจจะซับซ้อนเกินไป 
แต่ถ้าเป็นระบบที่มี **Workflow ซับซ้อน หรือ ต้องพัฒนาในระยะยาว** 
การใช้ Clean Architecture จะช่วยให้โค้ดสะอาดและขยายได้ง่าย! 🚀
