terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
  required_version = ">=1.8.4" /*Многострочный комментарий.
 Требуемая версия terraform */
}

#provider "docker" {
#  host = "ssh://user@158.160.139.48"
#}

#resource "random_password" "random_string" {
#  length      = 16
#  special     = false
#  min_upper   = 1
#  min_lower   = 1
#  min_numeric = 1
#}

#resource "docker_image" "nginx" { # Указано имя ресурса
#  name         = "nginx:latest"
#  keep_locally = true
#}

#resource "docker_container" "hello_world" {
#  image = docker_image.nginx.image_id
#  name  = "example_${random_password.random_string.result}" # Исправлена ссылка на random_password

#  ports {
#    internal = 80
#    external = 9090
#  }
#}

provider "docker" {
  host = "ssh://user@158.160.139.48"  # Замените на IP вашей ВМ
}

# Генерация пароля для root MySQL
resource "random_password" "mysql_root_password" {
  length  = 16
  special = true
  upper   = true
  lower   = true
  numeric = true
}

# Генерация пароля для пользователя MySQL
resource "random_password" "mysql_user_password" {
  length  = 16
  special = true
  upper   = true
  lower   = true
  numeric = true
}

# Создание Docker-контейнера с MySQL
resource "docker_container" "mysql" {
  name  = "mysql_container"
  image = "mysql:8"
  ports {
    internal = 3306
    external = 3306
  }

  env = [
    "MYSQL_ROOT_PASSWORD=${random_password.mysql_root_password.result}",
    "MYSQL_DATABASE=wordpress",
    "MYSQL_USER=wordpress",
    "MYSQL_PASSWORD=${random_password.mysql_user_password.result}",
    "MYSQL_ROOT_HOST=%"
  ]

  restart = "unless-stopped"
}
