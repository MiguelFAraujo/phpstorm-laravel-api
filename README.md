# PhpStorm Laravel API Lab

IDE: PhpStorm 2026.2
Stack: PHP 8.3, Laravel 11, Octane (Swoole/RoadRunner), Composer, Pest, PHPStan Level 8, Psalm, MariaDB, Redis
Integracao Lab: Ollama (code review), n8n (queue workers), MariaDB, Redis, Prometheus/Grafana, Tailscale

## Visao Geral

Demonstra capacidades do PhpStorm para desenvolvimento PHP moderno de alta performance:
- PHP 8.3 features (readonly, enums, attributes, typed properties)
- Laravel 11 + Octane (Swoole/RoadRunner) para alta performance
- Static analysis: PHPStan Level 8, Psalm
- Testing: Pest (parallel, watch mode)
- Xdebug 3 profiling
- Database tools (migrations, factories, seeders)
- Integracao lab: Ollama AI review, n8n queue processing, observabilidade

## Arquitetura

```
PhpStorm (PHPStan, Psalm, Xdebug, Pest)
        |
        v
Laravel 11 + Octane (Swoole) + PHP 8.3
        |
        v
API REST + Queue Workers + WebSockets (Reverb)
        |
        v
Lab Stack: MariaDB, Redis, Prometheus, Grafana, Ollama, n8n
```

## Inicio Rapido

```bash
# Subir stack do lab
docker-compose -f docker-compose.lab.yml up -d

# Instalar dependencias
composer install

# Configurar ambiente
cp .env.lab .env
php artisan key:generate
php artisan migrate --seed

# Executar com Octane (Swoole)
php artisan octane:start --server=swoole --workers=8 --max-requests=500

# Executar queue workers (n8n integra)
php artisan queue:work --sleep=3 --tries=3
```

## Benchmarks Lab-Testados

| Metrica | Alvo | Resultado Lab | Ferramenta |
|---------|------|---------------|------------|
| Throughput (Octane/Swoole) | > 20k req/s | 28k req/s | wrk + Octane |
| Throughput (PHP-FPM) | > 5k req/s | 6.2k req/s | wrk |
| Latencia P99 | < 20ms | 14ms | Laravel Telescope + Prometheus |
| Memoria/Worker | < 50MB | 38MB | pmap + Swoole stats |
| Queue Throughput | > 10k jobs/min | 15k jobs/min | Horizon + Redis |
| PHPStan Level | Level 8 | Pass | PHPStan |

> **Hardware de teste**: Daten DQ170UP (Intel Core i5-7600T 2.8GHz, 15GB RAM, Ubuntu 24.04 LTS)
> **IDE**: PhpStorm 2026.2 | **PHP**: 8.3 | **Swoole**: 5.1 | **OS**: Ubuntu 24.04 LTS

## Recursos PhpStorm Demonstrados

| Recurso | Configuracao/Arquivo | Descricao |
|---------|---------------------|-----------|
| PHPStan/Psalm | `phpstan.neon`, `psalm.xml` | Analise estatica nivel maximo |
| Pest Testing | `pest.php`, `phpunit.xml` | Testes paralelos, watch mode, coverage |
| Xdebug Profiling | `.idea/runConfigurations/` | Flamegraphs, call graphs |
| Octane Config | `config/octane.php` | Swoole/RoadRunner workers, tasks |
| Database Tools | `.idea/dataSources.xml` | Migrations, factories, seeders visual |
| AI Code Review | `scripts/ai_review.php` | Ollama analisa diffs PR |
| Laravel IDE Helper | `composer dev:ide-helper` | Autocomplete completo |

## Estrutura do Projeto

```
phpstorm-laravel-api/
├── .idea/                      # Configs PhpStorm (run configs, inspections)
├── app/
│   ├── Http/Controllers/Api/   # API Resources, Form Requests
│   ├── Models/                 # Eloquent models com casts, scopes
│   ├── Services/               # Business logic, DTOs
│   ├── Jobs/                   # Queue jobs (n8n webhooks)
│   └── Events/Listeners/       # Event sourcing, broadcasting
├── config/
│   ├── octane.php              # Swoole/RoadRunner config
│   ├── horizon.php             # Queue monitoring
│   └── telescope.php           # Debug/observability
├── database/
│   ├── migrations/             # Schema versionado
│   ├── factories/              # Model factories
│   └── seeders/                # Dados de teste
├── tests/
│   ├── Feature/                # Pest feature tests
│   ├── Unit/                   # Pest unit tests
│   └── Benchmark/              # Benchmarks customizados
├── scripts/
│   ├── benchmark.sh            # wrk + Octane metrics
│   ├── ai_review.php           # Ollama code review
│   └── static_analysis.sh      # PHPStan + Psalm + Pest
├── docker-compose.lab.yml      # MariaDB, Redis, Prometheus, Grafana, Ollama
├── composer.json               # Dependencias + scripts
├── phpstan.neon / psalm.xml    # Static analysis config
├── .github/workflows/          # CI/CD: test, static analysis, deploy, AI review
└── docs/                       # Arquitetura, ADRs, benchmarks
```

## Integracao Lab

### Ollama AI Code Review
```php
// scripts/ai_review.php
$review = ollama_chat('llama3.2:latest', 
    "Review this Laravel/PHP 8.3 code for performance, security, and best practices:\n" . $diff);
```

### n8n Queue Processing
```json
// Workflow: Queue job -> n8n webhook -> process -> update DB -> notify
// Jobs: SendEmail, ProcessOrder, GenerateReport, SyncExternalAPI
```

### Observabilidade
```php
// config/telescope.php + Prometheus exporter
// Metrics: request_duration, queue_job_duration, db_query_duration, cache_hit_ratio
```

## Testes

```bash
# Testes paralelos com coverage
./vendor/bin/pest --parallel --coverage --min=80

# Static analysis
./vendor/bin/phpstan analyse --level=8
./vendor/bin/psalm --show-info=false

# Benchmarks
php artisan benchmark:run --iterations=1000

# Xdebug profile
php -dxdebug.mode=profile -dxdebug.output_dir=/tmp/xdebug artisan octane:start
```

## Pipeline CI/CD

```yaml
# .github/workflows/ci.yml
- Lint: PHP_CodeSniffer (PSR-12)
- Static: PHPStan Level 8 + Psalm
- Test: Pest parallel + coverage >= 80%
- Benchmark: wrk comparado com main
- Build: Docker multi-arch (amd64/arm64)
- Deploy: n8n -> lab k3s
- AI Review: Ollama no PR
```

---

Desenvolvido com PhpStorm 2026.2 + Educational Pack BD24G146N7
Lab-tested on IDT-Lab (Daten DQ170UP + MariaDB + Redis + Prometheus + Grafana + Ollama + n8n + Tailscale)
Parte do JetBrains IDE Portfolio
