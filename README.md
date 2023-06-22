# info

```sh
#!/bin/zsh

# Script params
aws_profile=$1
searching_resource=$2

# Result file
result_file=result_$(date +"%s").txt

# Find all roles in AWS project
aws iam --profile $aws_profile list-roles > /tmp/roles.json
arr_roles=( $(jq '.Roles[].RoleName' /tmp/roles.json) )

for role in "${arr_roles[@]}"; do

  # Find all policies for choose role
  aws iam --profile $aws_profile list-role-policies \
    --role-name $role > /tmp/policies.json
  arr_policies=( $(jq '.PolicyNames[]' /tmp/policies.json) )

  for policy in "${arr_policies[@]}"; do

    # Find all permissions for choose role and policy
    aws iam --profile $aws_profile get-role-policy \
      --role-name $role \
      --policy-name $policy > /tmp/role_policies.json

    # Check is permission containg search params
    if grep -iq $searching_resource /tmp/role_policies.json; then
      echo "Find for role $role in policy $policy."
      echo "$role : $policy" >> $result_file
      cat /tmp/role_policies.json >> $result_file
    else
      echo "Policy $policy in role $role is empty."
    fi
  done
done
```
