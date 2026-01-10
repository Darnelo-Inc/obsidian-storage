Ansible Vault — это инструмент для шифрования конфиденциальных данных (пароли, ключи, токены) в Ansible.

## Основные команды

##### Создать зашифрованный файл
`ansible-vault create secrets.yml`
##### Зашифровать существующий файл
`ansible-vault encrypt vars.yml`
##### Расшифровать файл
`ansible-vault decrypt secrets.yml`
##### Редактировать зашифрованный файл
`ansible-vault edit secrets.yml`
##### Просмотреть содержимое
`ansible-vault view secrets.yml`
##### Изменить пароль
`ansible-vault rekey secrets.yml`

##### Использование в playbook

1. Создать файл с секретами
`ansible-vault create group_vars/all/vault.yml`
Содержимое:
`vault_db_password: "super_secret_password"
vault_api_key: "abc123xyz`"

##### 2. Использовать в playbook

- hosts: webservers
  vars_files:
    - group_vars/all/vault.yml
  tasks:
    - name: Настроить БД
      mysql_user:
        name: myuser
        password: "{{ vault_db_password }}"

##### 3. Запуск с запросом пароля
`ansible-playbook site.yml --ask-vault-pass`
##### Файл с паролем

##### Создать файл пароля
`echo "my_vault_password" > ~/.vault_pass
`chmod 600 ~/.vault_pass``

##### Использовать файл пароля
`ansible-playbook site.yml --vault-password-file ~/.vault_pass`
##### Настроить в ansible.cfg
`[defaults]
vault_password_file = ~/.vault_pass`
##### Шифрование отдельных переменных

##### Зашифровать строку
`ansible-vault encrypt_string 'secret_password' --name 'db_password'`
Вывод:
`db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          66386439653765386661326632323264...`
###### Использование в файле
vars/main.yml
db_user: admin
db_password: !vault |
    $ANSIBLE_VAULT;1.1;AES256
          66386439653765386661326632323264...

##### Множественные vault паролей

##### Создать vault ID
`ansible-vault create --vault-id prod@prompt secrets_prod.yml
`ansible-vault create --vault-id dev@prompt secrets_dev.yml
###### Использование
`ansible-playbook site.yml --vault-id prod@~/.vault_pass_prod --vault-id dev@prompt`