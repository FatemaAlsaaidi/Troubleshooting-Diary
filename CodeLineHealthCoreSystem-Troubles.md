# Troubleshooting Report CodeLineHealthCoreSystem Implementaion
## 1.Role Reset Issue for SuperAdmin After Loading Data
#### *Problem Description*
- The SuperAdmin objects lose their Role value (Super Admin) after saving to a JSON file and loading it back. When the program reloads data, the Role property becomes 'Guest', preventing proper role-based login and causing the SuperAdmin user to be treated as a Guest.
#### *Root Cause*
- During JSON deserialization, the default constructor of SuperAdmin is called. This default constructor calls the base User class default constructor, which sets Role = 'Guest'. Because JSON deserialization does not use parameterized constructors, if the Role property is missing or empty in the JSON file, the Role value stays as 'Guest'.
