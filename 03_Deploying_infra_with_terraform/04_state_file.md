# state file  

When we are using any application
WEBSITE <-> DATABASE

When users sign up to the website, they provide username and password
TO remember the username and password, we need to make use of database. 

If the database is down or destoryed, then the user information is lost.

### state file 
- Similarly terraform stores the information about resources it creates in state file
- With state file it will identify which resource it should modify, destroy.
- We should never modify the state file manually
- State file exists in JSON format.
- By default terraform looks for state file with name terraform.tfstate.
- If we rename the file, then terraform cannot identify resources.
- There are state commands which we can use to modify the state files.
- There would also be a backup of the state file, But it is just fail safe mechanism,.
- We should implement external backup options like s3 bucket versioning.
