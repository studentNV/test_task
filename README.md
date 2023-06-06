# test_task
Comand for start ELKF version 6.7.2
```bash
ansible-playbook --extra-vars "docker_image_version=6.7.2" -i inventory.yaml site.yaml -D
```
Comand for start ELKF version 7.7.0
```bash
ansible-playbook --extra-vars "docker_image_version=7.7.0" -i inventory.yaml site.yaml -D
```