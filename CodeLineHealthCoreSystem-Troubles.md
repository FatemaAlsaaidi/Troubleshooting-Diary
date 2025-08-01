# Troubleshooting Report CodeLineHealthCoreSystem Implementaion
## 1.Role Reset Issue for SuperAdmin After Loading Data
#### *Problem Description*
- The SuperAdmin objects lose their Role value (Super Admin) after saving to a JSON file and loading it back. When the program reloads data, the Role property becomes 'Guest', preventing proper role-based login and causing the SuperAdmin user to be treated as a Guest.
#### *Root Cause*
- During JSON deserialization, the default constructor of SuperAdmin is called. This default constructor calls the base User class default constructor, which sets Role = 'Guest'. Because JSON deserialization does not use parameterized constructors, if the Role property is missing or empty in the JSON file, the Role value stays as 'Guest'.
#### *Why This Happens*
1. A SuperAdmin object is created using the parameterized constructor, which sets Role = 'Super Admin'.
2. The object is saved to a JSON file.
3. When loading, the JSON serializer calls the default constructor (not the parameterized one).
4. The default constructor sets Role = 'Guest' (inherited from the User class).
5. If the JSON file does not explicitly store Role or has null/empty, the value remains 'Guest'.

#### *Solutions*
- Option 1: Ensure Role is Always Saved in JSON File.
Before saving, check and assign the correct Role value:
```sql
if (string.IsNullOrEmpty(superAdmin.Role))
    superAdmin.Role = "Super Admin";
```

- Option 2: Set Role in the Default Constructor of SuperAdmin.
```sql
public SuperAdmin() : base()
{
    Role = "Super Admin";
}
```

- Option 3: Use JSON Attributes to Always Serialize Role.
[JsonInclude]
``` sql
public string Role { get; set; } = "Super Admin";
```
#### *Recommended*
1. Always include Role when saving SuperAdmin data to the JSON file using JsonSerializer.Serialize.
2. Add Role = "Super Admin" in the default constructor of SuperAdmin as a safety net.

#### *Code Example* 
- Default Constructor Fix:
```sql
public SuperAdmin() : base()
{
    Role = "Super Admin"; // Ensure correct role when deserializing
}
```
- Saving Data to JSON File:
```sql
File.WriteAllText("superadmins.json", JsonSerializer.Serialize(SuperAdmin.SuperAdmins));
```

- Loading Data from JSON File:
```sql
SuperAdmin.SuperAdmins = JsonSerializer.Deserialize<List<SuperAdmin>>(File.ReadAllText("superadmins.json"));
```
#### *Summary*

- The issue occurs because JSON deserialization uses the default constructor of SuperAdmin, which calls the User default constructor setting Role = 'Guest'. To fix this, always ensure Role is saved in the JSON file or set Role in the default constructor of SuperAdmin. With this fix, loading SuperAdmin data will correctly preserve the role and allow proper role-based login


## 2. GitHub Merge Conflict Resolution Guide Issue
#### *Overview*
- Resolve a GitHub merge conflict that occurred in the file Models/UsersMenu.cs due to modifications in the same lines of code.
#### *Conflict Markers*
- When a conflict happens, Git adds special markers in the file to indicate the differences:
``` sql
<<<<<<< master
// code from your branch (FatemaAlsaaidi:master)
=======
// code from other branch (DeveloperZahra:master)
>>>>>>> master
```
- The part between <<<<<<< master and ======= is your version.
- The part between ======= and >>>>>>> master is the other branch version.




