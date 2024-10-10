### **Externalization:**

**Definition:**
Externalization is a more advanced serialization mechanism that allows developers to explicitly define how an object’s state is saved and restored. This is done by implementing the `Externalizable` interface, which requires overriding two methods: `writeExternal()` and `readExternal()`.

**Use Cases:**
- **Fine-Grained Control:** When you need to control what data gets serialized (e.g., omitting certain fields or serializing only a portion of the object).
- **Custom Serialization Format:** When you need to define a specific format for the serialized data (e.g., for compatibility with systems that expect data in a certain format).

**How It Works:**
- Implementing the `Externalizable` interface gives developers full control over what fields get serialized and how they are deserialized.

**Advantages:**
1. **Control:** You can choose what data to serialize and how to serialize it, giving better control over the object’s byte representation.
2. **Efficiency:** You can optimize the serialization process by including only the necessary fields, improving performance.

**Disadvantages:**
1. **Manual Effort:** You must explicitly define the logic for reading and writing each field, which can be error-prone.
2. **More Code:** Externalization requires additional code to manage serialization and deserialization, compared to the automatic behavior of `Serializable`.

**Example:**
```java
import java.io.*;

class Employee implements Externalizable {
    private String name;
    private int age;

    public Employee() {
        // No-arg constructor needed for deserialization
    }

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public void writeExternal(ObjectOutput out) throws IOException {
        out.writeObject(name);
        out.writeInt(age);
    }

    @Override
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
        name = (String) in.readObject();
        age = in.readInt();
    }

    @Override
    public String toString() {
        return "Employee{" + "name='" + name + '\'' + ", age=" + age + '}';
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        Employee emp = new Employee("John", 30);

        // Externalization (Serialization)
        FileOutputStream fos = new FileOutputStream("employee_ext.ser");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        emp.writeExternal(oos);
        oos.close();

        // Externalization (Deserialization)
        FileInputStream fis = new FileInputStream("employee_ext.ser");
        ObjectInputStream ois = new ObjectInputStream(fis);
        Employee deserializedEmp = new Employee();
        deserializedEmp.readExternal(ois);
        ois.close();

        System.out.println(deserializedEmp);
    }
}
```

### **Key Differences Between Serialization and Externalization:**

| Feature                | **Serialization**                         | **Externalization**                         |
|------------------------|-------------------------------------------|---------------------------------------------|
| **Interface**           | `Serializable`                           | `Externalizable`                            |
| **Automatic Process**   | Yes (for non-transient fields)            | No (manual serialization of fields)         |
| **Customization**       | Limited (via `transient`, `serialVersionUID`) | Full control via `writeExternal`, `readExternal` |
| **Performance**         | Slower (includes all fields)              | Faster (you can choose what to serialize)   |
| **Control**             | Less control over the process             | Complete control over what to serialize     |
| **Backward Compatibility** | Built-in (using `serialVersionUID`)     | Must handle versioning manually             |

To prevent certain fields from being serialized using **Externalization**, you can manually control the fields that are written and read during the serialization process. In this case, you want to exclude the `bank_acc_no` field from serialization.

Here's how you can achieve that:

### **Steps to Exclude `bank_acc_no` Using Externalization:**

1. **Implement the `Externalizable` interface** instead of `Serializable`.
2. In the `writeExternal()` method, explicitly write only the fields you want to serialize (i.e., exclude `bank_acc_no`).
3. In the `readExternal()` method, explicitly read those fields in the same order they were written.

### **Code Example:**

```java
import java.io.*;

class Person implements Externalizable {
    private String name;
    private int age;
    private String gender;
    private String email;
    private String address;
    private transient String bankAccNo;  // Exclude this from serialization

    // Default constructor (required for Externalizable)
    public Person() {}

    public Person(String name, int age, String gender, String email, String address, String bankAccNo) {
        this.name = name;
        this.age = age;
        this.gender = gender;
        this.email = email;
        this.address = address;
        this.bankAccNo = bankAccNo;
    }

    // Implement the writeExternal() method to control what gets serialized
    @Override
    public void writeExternal(ObjectOutput out) throws IOException {
        out.writeObject(name);
        out.writeInt(age);
        out.writeObject(gender);
        out.writeObject(email);
        out.writeObject(address);
        // Skip bankAccNo (do not write it)
    }

    // Implement the readExternal() method to control what gets deserialized
    @Override
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
        name = (String) in.readObject();
        age = in.readInt();
        gender = (String) in.readObject();
        email = (String) in.readObject();
        address = (String) in.readObject();
        // Skip bankAccNo (do not read it, so it remains null)
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", gender='" + gender + '\'' +
                ", email='" + email + '\'' +
                ", address='" + address + '\'' +
                ", bankAccNo='" + (bankAccNo == null ? "not serialized" : bankAccNo) + '\'' +
                '}';
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Person person = new Person("John Doe", 30, "Male", "john@example.com", "123 Street", "1234567890");

        // Serialization
        FileOutputStream fos = new FileOutputStream("person.ser");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(person);
        oos.close();

        // Deserialization
        FileInputStream fis = new FileInputStream("person.ser");
        ObjectInputStream ois = new ObjectInputStream(fis);
        Person deserializedPerson = (Person) ois.readObject();
        ois.close();

        System.out.println(deserializedPerson); // The bankAccNo will be null/not serialized
    }
}
```

### **Explanation:**

1. **Field Control:** Since you are using `Externalizable`, you explicitly control what gets serialized and deserialized by including/excluding fields in the `writeExternal()` and `readExternal()` methods. In this case, we exclude `bankAccNo` from both methods, so it is not serialized.

2. **Transient Keyword:** While `Externalization` itself gives full control over which fields are written/read, marking the `bankAccNo` field as `transient` further emphasizes that it is not intended for serialization.

3. **Deserialization:** During deserialization, the `bankAccNo` field is not restored and will remain `null` or unset.

### **Advantages of Externalization in This Case:**

- You have **complete control** over which fields get serialized, so sensitive fields (like `bankAccNo`) are easily excluded.
- The **serialized format** is more compact, as unnecessary fields are not serialized, leading to performance improvements.

### **Disadvantages:**

- Requires **manual handling** of all fields, which can be error-prone if the class has many fields or changes frequently.
- If fields are added or removed, you must **manually adjust** the `writeExternal()` and `readExternal()` methods.

This is how you can exclude sensitive fields like `bank_acc_no` from serialization using **Externalization** in Java.
