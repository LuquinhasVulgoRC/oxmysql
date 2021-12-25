### oxmysql vRPEX

## Installation

#### 1. Open **vrp/cfg/base.lua**
#### 2. Change a **driver** key of the object to **oxmysql**

```lua
{
	driver = "oxmysql",
	host = "localhost",
	database = "database",
	user = "root",
	password = "password"
}
```

### 3. Open your vrp folder
### 4. Open **vrp/base.lua**.
### 5. In the declared function vRP.getUserIdByIdentifiers, change the order of receipts of the vRP/create_users query.
### 6. Change this if the function is equivalent, the query may be different, so if it's not the default query, only change it if you understand what you're doing.

```lua
function vRP.getUserIdByIdentifiers(ids)
	if ids and #ids then
		for i=1,#ids do
			if (string.find(ids[i],"ip:") == nil) then
				local rows = vRP.query("vRP/userid_byidentifier",{ identifier = ids[i] })
				if #rows > 0 then
					return rows[1].user_id
				end
			end
		end

		-- BEFORE ---> local rows,affected = vRP.query("vRP/create_user",{})

		local affected, rows = vRP.query("vRP/create_user",{})
		-- AFTER ^

		if #rows > 0 then
			local user_id = rows[1].id
			for l,w in pairs(ids) do
				if (string.find(w,"ip:") == nil) then
					vRP.execute("vRP/add_identifier",{ user_id = user_id, identifier = w })
				end
			end
			return user_id
		end
	end
end
```

### 7. Open your server.cfg or equivalent
### 8. Replace mysql_connection_string with the following code

```lua
set mysql_connection_string "mysql://root@localhost/database?charset=utf8mb4"
```

### 9. Replace "database" with the name of your database

## End of tutorial