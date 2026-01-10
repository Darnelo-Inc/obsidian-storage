```yaml
---

- name: Test connection to my servers
  hosts: "{{ HOSTS }}"
  become: yes # запуск от рута

  vars:
	owner: "{{ OWNER }}"
  
  tasks:
	- name: Ping my servers
	  ping:

...
```

Запускаем командой `ansible-playbook ping-servers.yaml -e "HOSTS=ALL OWNER=DARNELO"`

---

```yaml
---

- name: Install Apache
  hosts: all
  become: yes # запуск от рута

  vars:
	source_file: ./MyWebSite/index.html
	destin_file: /var/www/html
  
  tasks:
	- name: Ping my servers
	  ping:
	  delegate_to: host_5 # запуск таски только на host_5

	- name: Install Apache
	  yum: name=httpd state=latest
	
	- name: Copy html
	  copy: name={{ source_file }} dest={{ destin_file }} mode=0555 
	  notify: Restart Apache

	- name: Start Apache
	  service: name=httpd state=started enabled=yes

	# Для рестарта apache при изменении html файла добавим handler

  handlers:
	- name: Restart Apache
	  server: name=httpd state=restarted

...
```

---

Пример с delegate_to

```yaml
---

- name: Убрать сервер из балансировщика
  command: remove_from_pool {{ inventory_hostname }}
  delegate_to: load_balancer

- name: Обновить приложение
  apt:
    name: myapp
    state: latest

- name: Вернуть сервер в балансировщик
  command: add_to_pool {{ inventory_hostname }}
  delegate_to: load_balancer

...
```




