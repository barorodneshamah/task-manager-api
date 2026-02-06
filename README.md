# Task Manager API

A simple REST API for managing tasks built with Symfony and API Platform.

## Prerequisites

- Docker & Docker Compose
- PHP 8.2+
- Composer

## Setup Instructions

### 1. Install Symfony locally
```bash
composer create-project symfony/skeleton:"6.4.*" task-manager-api
cd task-manager-api
```

### 2. Start Docker Containers
```bash
New-Item docker-compose.yml
```
Create:
services:
  mysql:
    image: mysql:8.0
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: task_manager_api
      MYSQL_USER: symfony_user
      MYSQL_PASSWORD: symfony_pass
    ports:
    - "3307:3306"        
    volumes:
    - mysql_data:/var/lib/mysql
    networks:
    - test_demo_network

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: mysql
      PMA_USER: symfony_user
      PMA_PASSWORD: symfony_pass
    ports:
      - "8080:80"
    depends_on:
      - mysql
    networks:
    - test_demo_network

volumes:
  mysql_data:

networks:
  test_demo_network:
    driver: bridge

### 3. Configure Environment

```env
DATABASE_URL="mysql://symfony_user:symfony_pass@mysql:3306/task_manager_api?serverVersion=8.0&charset=utf8mb4"
```

```bash
docker-compose up -d
```

### 4. Install Dependencies
```bash
composer require api-platform/core
composer require symfony/orm-pack
composer require --dev symfony/maker-bundle
```
### 5. Create Entity
```bash
 symfony console make:entity Task       
```
 created: src/Entity/Task.php
 created: src/Repository/TaskRepository.php

 Entity generated! Now let's add some fields!
 You can always add more fields later manually or by re-running this command.

 New property name (press <return> to stop adding fields):
 > title

 Field type (enter ? to see all types) [string]:
 > string

 Field length [255]:
 > 255

 Can this field be null in the database (nullable) (yes/no) [no]:
 > no

 updated: src/Entity/Task.php

 Add another property? Enter the property name (or press <return> to stop adding fields):
 > description

 Field type (enter ? to see all types) [string]:
 > text

 Can this field be null in the database (nullable) (yes/no) [no]:
 > yes

 updated: src/Entity/Task.php

 Add another property? Enter the property name (or press <return> to stop adding fields):
 > isCompleted

 Field type (enter ? to see all types) [boolean]:
 > boolean

 Can this field be null in the database (nullable) (yes/no) [no]:
 > no

 updated: src/Entity/Task.php

 Add another property? Enter the property name (or press <return> to stop adding fields):
 > createdAt

 Field type (enter ? to see all types) [datetime_immutable]:
 > datetime_immutable

 Can this field be null in the database (nullable) (yes/no) [no]:
 > no

 updated: src/Entity/Task.php

 Add another property? Enter the property name (or press <return> to stop adding fields):
 >

  Success! 
 
### 6. Configure the Entity Add:
use Doctrine\ORM\Mapping as ORM;
use ApiPlatform\Metadata\ApiResource;
use ApiPlatform\Metadata\Get;
use ApiPlatform\Metadata\GetCollection;
use ApiPlatform\Metadata\Post;
use ApiPlatform\Metadata\Put;
use ApiPlatform\Metadata\Delete;

use Symfony\Component\Serializer\Attribute\Groups;

#[ApiResource(
    operations: [
        new Get(),
        new GetCollection(),
        new Post(),
        new Put(),
        new Delete()
    ],
    normalizationContext: [
        'groups' => ['task:read']
    ],
    denormalizationContext: [
        'groups' => ['task:write']
    ]
)]
#[ORM\Entity(repositoryClass: TaskRepository::class)]
#[ORM\HasLifecycleCallbacks]
class Task
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    #[Groups(['task:read'])]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    #[Groups(['task:read', 'task:write'])]
    private ?string $title = null;

    #[ORM\Column(type: Types::TEXT, nullable: true)]
    #[Groups(['task:read', 'task:write'])]
    private ?string $description = null;

    #[ORM\Column]
    #[Groups(['task:read', 'task:write'])]
    private ?bool $isCompleted = null;

    #[ORM\Column(nullable: false, updatable: false)]
    #[Groups(['task:read'])]
    private ?\DateTimeImmutable $createdAt = null;


```bash
 symfony console make:migration
 symfony console doctrine:migrations:migrate
```


### 7. Start Symfony Server
```bash
symfony server:start
# Or
php -S localhost:8000 -t public/
```

### 8. Go to the Browser
- **API Documentation**: https://127.0.0.1:8000/api
- **phpMyAdmin**: http://localhost:8080
  - Username: `symfony_user`
  - Password: `symfony_pass`

## Using API Platform Interface

1. Open http://127.0.0.1:8000/api in your browser
2. Click on any endpoint (e.g. POST,GET,PUT,DELETE)
3. Click "Try it out"
4. Enter your data and click "Execute"

### Example: Create a Task (POST)

**Request:**
```json
{
  "title": "Complete project documentation",
  "description": "Write comprehensive README and setup guides"
}
```

**Response:**
```json
{
  "@context": "/api/contexts/Task",
  "@id": "/api/tasks/1",
  "@type": "Task",
  "id": 1,
  "title": "Complete project documentation",
  "description": "Write comprehensive README and setup guides",
  "isCompleted": false,
  "createdAt": "2026-02-06T07:09:30+00:00"
}
```

### Example: Mark Task as Completed (PATCH)

**Request:**
```json
{
  "isCompleted": true
}
```

### Using cURL
```bash
# Create a task
curl -X POST http://127.0.0.1:8000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"My First Task","description":"Testing the API"}'

# Get all tasks
curl http://127.0.0.1:8000/api/tasks

# Get a specific task
curl http://127.0.0.1:8000/api/tasks/1

# Update a task
curl -X PATCH http://127.0.0.1:8000/api/tasks/1 \
  -H "Content-Type: application/merge-patch+json" \
  -d '{"isCompleted":true}'

# Delete a task
curl -X DELETE http://127.0.0.1:8000/api/tasks/1
```
### 8. Clone the Repository for Github

```bash
git clone https://github.com/your-username/task-manager-api.git
cd task-manager-api







# task-manager-api
