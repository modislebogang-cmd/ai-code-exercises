# Findings for Products API OpenAPI Documentation

```yaml
openapi: 3.0.3
info:
  title: Product Listing API
  version: 1.0.0
  description: |
    Retrieve a paginated list of products with optional filtering, sorting, and stock availability.
servers:
  - url: https://api.example.com
    description: Production server

paths:
  /api/products:
    get:
      summary: List Products
      description: Get a list of products with flexible filtering, sorting, and pagination.
      operationId: listProducts
      parameters:
        - name: category
          in: query
          description: Filter products by category.
          required: false
          schema:
            type: string
        - name: minPrice
          in: query
          description: Filter products with price greater than or equal to this value.
          required: false
          schema:
            type: number
            format: float
            minimum: 0
        - name: maxPrice
          in: query
          description: Filter products with price less than or equal to this value.
          required: false
          schema:
            type: number
            format: float
            minimum: 0
        - name: sort
          in: query
          description: Field to sort by.
          required: false
          schema:
            type: string
            default: createdAt
        - name: order
          in: query
          description: Sort order: `asc` or `desc`.
          required: false
          schema:
            type: string
            enum: [asc, desc]
            default: desc
        - name: page
          in: query
          description: Page number for pagination.
          required: false
          schema:
            type: integer
            format: int32
            minimum: 1
            default: 1
        - name: limit
          in: query
          description: Number of items per page (maximum 100).
          required: false
          schema:
            type: integer
            format: int32
            minimum: 1
            maximum: 100
            default: 20
        - name: inStock
          in: query
          description: When `true`, only show products with stock > 0.
          required: false
          schema:
            type: boolean
      responses:
        '200':
          description: A paginated list of products.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ProductsResponse'
              examples:
                electronicsCategory:
                  summary: Products filtered by electronics category.
                  value:
                    products:
                      - _id: "61fa9bcf5c130b2e6d675432"
                        name: "Wireless Headphones"
                        description: "High-quality wireless headphones with noise cancellation"
                        price: 89.99
                        category: "electronics"
                        stockQuantity: 45
                        createdAt: "2023-02-01T15:32:47Z"
                        updatedAt: "2023-03-15T09:21:08Z"
                      - _id: "61fa9bcf5c130b2e6d675435"
                        name: "Bluetooth Speaker"
                        description: "Portable bluetooth speaker with 20 hour battery life"
                        price: 49.99
                        category: "electronics"
                        stockQuantity: 32
                        createdAt: "2023-01-25T14:22:19Z"
                        updatedAt: "2023-03-10T11:05:24Z"
                    pagination:
                      total: 12
                      page: 1
                      limit: 20
                      pages: 1
                priceRangeSorted:
                  summary: Products priced between $20 and $100, sorted by price ascending.
                  value:
                    products:
                      - _id: "61fa9bcf5c130b2e6d675439"
                        name: "T-Shirt"
                        description: "Cotton t-shirt with logo"
                        price: 24.99
                        category: "clothing"
                        stockQuantity: 150
                        createdAt: "2023-01-15T08:27:13Z"
                        updatedAt: "2023-01-15T08:27:13Z"
                      - _id: "61fa9bcf5c130b2e6d675435"
                        name: "Bluetooth Speaker"
                        description: "Portable bluetooth speaker with 20 hour battery life"
                        price: 49.99
                        category: "electronics"
                        stockQuantity: 32
                        createdAt: "2023-01-25T14:22:19Z"
                        updatedAt: "2023-03-10T11:05:24Z"
                    pagination:
                      total: 18
                      page: 1
                      limit: 10
                      pages: 2
        '500':
          description: Server error while fetching products.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
              example:
                error: "Server error"
                message: "Failed to fetch products"

components:
  schemas:
    Product:
      type: object
      properties:
        _id:
          type: string
          description: MongoDB ObjectId string.
        name:
          type: string
        description:
          type: string
        price:
          type: number
          format: float
        category:
          type: string
        stockQuantity:
          type: integer
          format: int32
        createdAt:
          type: string
          format: date-time
          description: ISO 8601 UTC timestamp.
        updatedAt:
          type: string
          format: date-time
          description: ISO 8601 UTC timestamp.
      required:
        - _id
        - name
        - description
        - price
        - category
        - stockQuantity
        - createdAt
        - updatedAt

    Pagination:
      type: object
      properties:
        total:
          type: integer
          format: int32
          description: Total number of matching products.
        page:
          type: integer
          format: int32
          description: Current page number.
        limit:
          type: integer
          format: int32
          description: Number of items returned per page.
        pages:
          type: integer
          format: int32
          description: Total number of pages available.
      required:
        - total
        - page
        - limit
        - pages

    ProductsResponse:
      type: object
      properties:
        products:
          type: array
          items:
            $ref: '#/components/schemas/Product'
        pagination:
          $ref: '#/components/schemas/Pagination'
      required:
        - products
        - pagination

    ErrorResponse:
      type: object
      properties:
        error:
          type: string
        message:
          type: string
      required:
        - error
        - message
```
