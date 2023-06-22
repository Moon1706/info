# info

```
#!/bin/bash

profile=xxxxxxx

aws iam --profile $profile list-roles > roles.json
arr_roles=( $(jq '.Roles[].RoleName' roles.json) )
for role in "${arr_roles[@]}"; do
  aws iam --profile $profile list-role-policies \
    --role-name $role > policies.json
  arr_policies=( $(jq '.PolicyNames[]' policies.json) )
  for policy in "${arr_policies[@]}"; do
    aws iam --profile $profile get-role-policy \
      --role-name $role \
      --policy-name $policy > role_policies.json
    if grep -iq lambda role_policies.json; then
      echo "$role : $policy"
    fi
  done
done
```
