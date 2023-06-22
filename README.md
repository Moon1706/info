# info

```
#!/bin/bash

profile=xxxxxxx
touch role_policies.json

aws iam --profile $profile list-roles > roles.json
arr_roles=( $(jq '.Roles[].RoleName' roles.json) )
for role in "${arr_roles[@]}"; do
  aws iam --profile $profile list-role-policies > policies.json
  arr_policies=( $(jq '.PolicyNames[]' policies.json) )
  for policy in "${arr_policies[@]}"; do
    echo $role >> role_policies.json
    aws iam --profile $profile get-role-policy \
      --role-name $role \
      --policy-name policy >> role_policies.json
  done
done
```
