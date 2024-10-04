# This is an example compose.yaml file to integrate the PostgreSQL to other applications.

services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: $(POSTGRES_USER)
      POSTGRES_PASSWORD: $[POSTGRES_PASSWORD)
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U cpow"]
      interval: 10s
      timeout: 5s
      retries: 5
  web:
    build:
    command: bash -c "rm -f tmo/pids/server.pid && bundle exec rails s -b 19.0.0.6'"
    volumes:
      -.: /rails
    ports:
      -3000: 3000
    depends_on:
      db:
        condition: service_healthy
    environment:
      DATABASE_HOST: $(DATABASE_HOST)
      DATABASE_USER: $(POSTGRES_USER)
      DATABASE_PASSWORD: S(POSTGRES_PASSWORD)

volumes:
  pgdata:
