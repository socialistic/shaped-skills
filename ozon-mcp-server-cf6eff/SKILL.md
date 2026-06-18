---
name: ozon-product-scout
description: |
  MCP-сервер для поиска товаров на Ozon — ищет по запросу, читает карточку товара
  и отзывы покупателей. Работает через headless Chromium, обходя антибот Ozon.
  Три инструмента: ozon_search, ozon_product_details, ozon_product_reviews.
original_repo: eduard256/ozon-mcp-server
original_author: eduard256
original_url: https://github.com/eduard256/ozon-mcp-server
conformed_by: socialistic.ai
conformed_at: 2026-06-18
---

# Ozon Product Scout

MCP-сервер, дающий ИИ-агенту прямой доступ к маркетплейсу Ozon (ozon.ru).
Позволяет искать товары, читать полные карточки и собирать отзывы покупателей —
всё через структурированный JSON, без парсинга HTML.

Публичного API для покупателей у Ozon нет, а сайт закрыт антиботом Variti.
Сервер держит один headless-браузер Chromium, проходит проверку один раз, затем
забирает данные JSON-ом из внутреннего `composer-api`. Первый запрос ~12 секунд
(прохождение антибота), далее 0.3–1 секунда.

## Инструменты

### ozon_search — поиск товаров

Ищет товары по текстовому запросу. Возвращает: название, цену (рубли, число),
старую цену, скидку, рейтинг, число отзывов, бренд, картинку и ссылку.

Параметры:
- `query` (обязателен) — поисковый запрос, например `"iphone 15"`, `"плед 150х200"`
- `sort` — сортировка: `popular` (по умолчанию), `price`, `price_desc`, `rating`, `new`, `discount`
- `priceMin`, `priceMax` — фильтр по цене в рублях
- `limit` — количество результатов (1–36, по умолчанию 12)

### ozon_product_details — карточка товара

Полная карточка по SKU, ссылке или slug: цена (с картой / без / старая),
наличие, рейтинг, продавец (имя + рейтинг), фото, характеристики, описание.

Параметры:
- `product` (обязателен) — SKU (`"1185261285"`), полная ссылка ozon.ru или slug

### ozon_product_reviews — отзывы покупателей

Настоящие отзывы: текст, оценка (1–5), плюсы, минусы, дата, полезность,
отметка о покупке, наличие фото.

Параметры:
- `product` (обязателен) — SKU, ссылка или slug
- `limit` — количество отзывов (1–30, по умолчанию 10)

## Как использовать

### Установка через Docker (рекомендуется)

```bash
docker run -i --rm --init --shm-size=1g eduard256/ozon-mcp-server:latest
```

Флаги обязательны: `-i` — stdin для stdio, `--init` — корректное завершение
Chromium, `--shm-size=1g` — память для браузера.

### Настройка в Claude Desktop / Claude Code

Добавьте в конфигурацию MCP-серверов:

```json
{
  "mcpServers": {
    "ozon": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "--init", "--shm-size=1g", "eduard256/ozon-mcp-server:latest"]
    }
  }
}
```

### Локальная установка

```bash
git clone https://github.com/eduard256/ozon-mcp-server.git
cd ozon-mcp-server
npm install
npx playwright install chromium
node src/index.js
```

## Типичные задачи

1. **Сравнение цен** — найти товар через `ozon_search`, затем `ozon_product_details`
   для каждого результата, сравнить цены с картой Ozon и без.
2. **Анализ отзывов** — `ozon_product_reviews` для сбора отзывов, выявления
   частых жалоб и преимуществ товара.
3. **Мониторинг скидок** — поиск с сортировкой `discount`, фильтрация по категории
   и ценовому диапазону.
4. **Выбор продавца** — сравнение рейтингов продавцов через карточки товара.

## Провенанс

- Оригинальный репозиторий: [eduard256/ozon-mcp-server](https://github.com/eduard256/ozon-mcp-server)
- Автор: eduard256
- Лицензия: MIT
- Язык реализации: JavaScript (Node.js ≥ 20)
- Зависимости: `@modelcontextprotocol/sdk`, `playwright`, `zod`
