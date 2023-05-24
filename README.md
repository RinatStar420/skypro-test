# Skypro Test Task

# Установка и запуск
## С использование docker
```bash
cat env_sample > .env  # change variables values if you need
docker-compose up -d
```

## без использования docker
```zsh
python -m venv .venv
. .venv/bin/activate
cat env_sample > .env  # change variables values if you need

poetry install
pre-commit install
skyeng migrate
skyeng runserver
```
