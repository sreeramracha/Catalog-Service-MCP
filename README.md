# Catalog Service MCP

A Java-based Model Context Protocol (MCP) server that exposes catalog operations for an LLM or other MCP client. It connects to a ShopNest-style catalog service and provides a small set of tools for listing categories, searching products, and retrieving a single product by ID.

## What it does

This server registers MCP tools over standard input/output transport:

- `list_categories` – lists all product categories and item counts
- `search_products` – searches products by price range with sorting and paging support
- `get_product` – fetches a single product by numeric ID

The implementation lives in:

- `src/main/java/com/project/catalogmcp/CatalogMcpServer.java`

## Project type

- Java 17
- Maven project
- Uses the MCP Java SDK
- Runs as a stdio MCP server

## Prerequisites

- Java 17 or newer
- Maven
- A running catalog service available at `CATALOG_URL` or at `http://localhost:8080`

## Build

From the project root:

```bash
mvn clean package
```

This produces a shaded runnable JAR under `target/`.

## Run

You can run the server directly with Java:

```bash
java -jar target/catalog-mcp-1.0.jar
```

Or run the main class via Maven:

```bash
mvn exec:java -Dexec.mainClass=com.project.catalogmcp.CatalogMcpServer
```

## Configuration

The server reads the catalog base URL from the environment variable `CATALOG_URL`:

```bash
export CATALOG_URL=http://localhost:8080
```

If not set, it defaults to:

```text
http://localhost:8080
```

## Example catalog API behavior

The server expects the remote catalog service to expose endpoints similar to:

- `/categories`
- `/products/search?size=20&page=0&sort=price,asc|minPrice=...&maxPrice=...`
- `/products/{id}`

## Project structure

```text
.
├── pom.xml
├── META-INF/
│   └── MANIFEST.MF
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── project/
│                   └── catalogmcp/
│                       └── CatalogMcpServer.java
├── target/
└── README.md
```

## Notes

The server is designed to be used by MCP-compatible clients and responds with formatted text results suitable for model consumption. All protocol communication is done over standard output, while logs are written to standard error to avoid interfering with the MCP stream.
