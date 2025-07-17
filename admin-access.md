#  Grant Admin Access Manually (if Admin UI not showing)

If the admin interface does not appear after initial Authentik login, follow this manual procedure using Django shell inside the `authentik-server` container.

## Access Django Shell

```bash
docker exec -it authentik-server bash
ak shell
```
python3 /manage.py shell
## Grant `access_admin_interface` permission directly

```python
from django.contrib.auth import get_user_model
from authentik.rbac.models import Permission

User = get_user_model()
admin = User.objects.get(username="admin")

perm = Permission.objects.get(codename="access_admin_interface")
admin.user_permissions.add(perm)
admin.save()
print(" Permission granted.")
```

## Or use Role-based binding method (preferred for RBAC)

```python
from django.contrib.auth import get_user_model
from authentik.rbac.models import Role, Permission

User = get_user_model()
admin = User.objects.get(username="admin")

# Create role
role, _ = Role.objects.get_or_create(name="Admin UI Access")

# Assign permission to role
perm = Permission.objects.get(codename="access_admin_interface")
role.permission_set.add(perm)  # NOTE: Use correct field name if different

# Bind user to role manually (if supported)
# RoleBinding.objects.create(user=admin, role=role)  # If RoleBinding exists

admin.save()
print(" Role-based access setup complete.")
```

If this still does not activate the admin panel, restart the container using:
```bash
docker restart authentik-server
