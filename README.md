# Домашнее задание к занятию «Управление доступом»

### Задание 1. Создайте конфигурацию для подключения пользователя

1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для подключения.
3. Создайте роли и все необходимые настройки для пользователя.
4. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (`kubectl logs pod <pod_id>`, `kubectl describe pod <pod_id>`).
5. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

------

### 1. Создайте конфигурацию для подключения пользователя

1. Используя OpenSSL создаём файл ключа:

![image](https://github.com/user-attachments/assets/7e963d53-5332-4d29-aa71-bf3e43a9a96f)

Создём запрос на подписание сертификата (CSR):

![image](https://github.com/user-attachments/assets/c7e97153-8fd2-47b7-b5e3-ac413e736fd6)

Генерируем файл сертификата (CRT). Поскольку используеися Microk8s, будем использовать ключи кластера по пути `/var/snap/microk8s/current/certs/`:

![image](https://github.com/user-attachments/assets/d4aeb8ca-aed8-4721-b1e0-58c47a44e9ea)

2. Настраиваем конфигурационный файл kubectl для подключения.

Создаём пользователя `pat` и настраивем его на использование созданного выше ключа:

![image](https://github.com/user-attachments/assets/3795ee24-6203-4105-bd6b-3da94964d4de)

Создаём новый контекст с именем `pat-context` и подключаем его к пользователю `pat`, созданному ранее:

![image](https://github.com/user-attachments/assets/915949b4-2219-4545-826c-c0fce4758207)

Проверяем, создался ли контекст:

![image](https://github.com/user-attachments/assets/d3c5c7f3-027a-4e8a-b325-4345d442ad97)

Контекст создался.

3. Для выполнения задания создадим отдельный Namespace:

![image](https://github.com/user-attachments/assets/130c8bfa-7084-4843-a4b0-5c8e58780595)

Также потребуется включение встроенного в Microk8s RBAC контроллера:

![image](https://github.com/user-attachments/assets/060e2d41-eb60-4c46-8206-f244d5a709f9)

Применяем манифест создания роли (Role) и манифест привязки роли к Namespace (RoleBinding):

![image](https://github.com/user-attachments/assets/e225a4eb-4a19-4621-970a-9ce4aa3185ba)
![image](https://github.com/user-attachments/assets/97477523-c44a-4ad2-b3a1-7275e63a8b95)

4. Для проверки прав пользователя переключаемся в его контекст:

![image](https://github.com/user-attachments/assets/4c9f444a-1bda-4b57-8ed6-d1f6c6ba74c7)

Развернём Pod в разрешенном для пользователя Namespace:

![image](https://github.com/user-attachments/assets/cde4cab1-a5b5-4b2f-b26c-1c65f6d4b94f)

Проверяем какие развернуты поды в Namespace с именем `default`:

![image](https://github.com/user-attachments/assets/9f4008dc-598e-478b-be4e-dc92ccf7c912)

Видим, что в Namespace с именем `default` нет доступа, так как он не был указан в манифесте Role.

Проверяем поды в Namespace с именем `access`, список подов отображается, т.к. на него есть разрешения в Role:

![image](https://github.com/user-attachments/assets/ec013e43-031c-4c86-9b6d-2c6ebe95cce5)

Также проверяем логи пода:

![image](https://github.com/user-attachments/assets/ad2f8f69-4749-4253-a338-591b979b8a5b)

Проверяем вывод описания пода:

![image](https://github.com/user-attachments/assets/2dfab900-bb62-43ce-af7c-54b3611e7b07)

Согласно манифесту роли и ее привязке к Namespace, у пользователя `pat` есть доступ подам, их логам и описанию.

5. Ссылка на манифест [Pod]()

   Ссылка на манифест [Role]()

   Ссылка на манифест [RoleBinding]()
