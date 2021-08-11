The attribute role should be assigned to the users and should be updated in the policy too.

for example 

```
spec:
  endpointSelector:
    matchLabels:
      role: admin-user
```

Here the role ```admin-user``` can only access the kl-integration. 

Such roles should be configured/assigned to the required users so that they can only access. 
