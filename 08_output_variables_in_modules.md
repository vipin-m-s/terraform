# Output variables in modules

- When we want to cross reference attributes in terraform
- If the resources are in the same project, It is possible
- But when the resources accessing the attributes are in different folder
- Then we cannot directly access the attributes.
- Since we do not know if the resource is created or not.
- Attributes are taken from the .tf state file

  <img width="1120" height="416" alt="Screenshot 2026-03-07 at 9 01 34 AM" src="https://github.com/user-attachments/assets/5bd0d4e2-dd95-4d39-a9f4-91d6bb851e33" />


  ## We have to utilize the output variables here

  <img width="1140" height="441" alt="Screenshot 2026-03-07 at 9 02 55 AM" src="https://github.com/user-attachments/assets/a1e5809f-5580-4d2c-a156-196829901994" />
