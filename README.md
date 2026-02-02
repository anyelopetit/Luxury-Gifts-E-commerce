# PRD: MVP E-Commerce Frontend - Tienda Virtual “Las 3 Hermanas"

## 1. Resumen Ejecutivo
**Nombre del producto:** Las 3 Hermanas  
**Versión:** MVP 1.0 (Solo Frontend)  
**Objetivo:** Demostrar una tienda virtual realista de productos varios, regalos, detalles y lujo (perfumes, carteras, tecnología). Generar 100 productos de prueba por categoría para simular escala real, con carrito persistente (localStorage), filtros avanzados y simulación de pago frontend.  
**Público objetivo:** Usuarios demo (clientes potenciales) y stakeholders (inversores/devs).  
**Alcance:** 100% frontend (React/Next.js recomendado), sin backend real. Datos mockeados localmente.  
**Fecha estimada de lanzamiento:** 2-3 semanas (1 dev full-time).  
**Métricas de éxito:** >95% carga <2s, carrito funcional en 100% browsers, navegación intuitiva (zero bugs en filtros/paginación).

## 2. Descripción del Producto
Una landing principal con catálogo completo, banners promocionales dinámicos, búsqueda/filtros/paginación. Carrito persistente via localStorage. Panel admin simple para CRUD de productos. Checkout simulado con formulario (datos ficticios) y "pago exitoso" mockeado. 

**Flujo usuario principal:**
1. Landing → Ver/Buscar productos.
2. Filtros/Orden → Seleccionar → Agregar al carrito.
3. Carrito → Checkout → Formulario → Simular pago → Confirmación.

**Productos demo (generados proceduralmente):**
- Categorías: Perfumes (100), Carteras (100), Tecnología (100), Regalos (100), Detalles (000). Total: 500 items.
- Datos realistas: Imágenes de Unsplash/Placeholders, títulos/descripciones variados (e.g., "Perfume Chanel No.5 100ml - Lujo Clásico"), precios $10-5000, stock/status aleatorios.

## 3. Requisitos Funcionales

### 3.1 Landing Principal
- **Catálogo de productos:** Grid responsive (3-4 cols desktop, 2 mobile). Cards con imagen, título, precio, rating (4.2/5 estrellas), "Agregar al carrito".
- **Búsqueda:** Barra superior con autocomplete (buscar por título/descripción). Resultados live.
- **Banner promociones:** Carousel superior (3-5 slides) con descuentos ficticios (e.g., "20% off en Perfumes Luxury").
- **Paginación:** Infinite scroll + botones (1-100 páginas). 20-50 items/página.
- **Filtros laterales (colapsables mobile):**
  | Filtro | Opciones |
  |--------|----------|
  | Categoría | Perfumes, Carteras, Tech, Regalos, Detalles |
  | Precio | $0-50, $50-200, $200+ (sliders) |
  | Rating | 4+, 3+, Todo |
  | Disponibilidad | En stock, Todos |
- **Ordenamiento:** Dropdown: Precio bajo-alto, alto-bajo, Popular, Nuevo, Rating.

### 3.2 Carrito (localStorage)
- Icono carrito (top-right) con badge contador items.
- Modal/Vista dedicada: Lista items (img, qty +/-, subtotal), total, "Proceder a pago".
- Persistencia: Sobrevive refresh/F5. Limpieza manual o post-pago.

### 3.3 Checkout y Pago Simulado
- Formulario: Nombre, Email, Dirección (Venezuela-focused: estados como Falcón), Teléfono, Método (Tarjeta/MercadoPago mock).
- Validación frontend (e.g., email válido, campos requeridos).
- Botón "Pagar": Spinner 2s → Modal "¡Pago exitoso! Orden #12345 confirmada." → Vaciar carrito.

### 3.4 Admin Panel
- Ruta protegida (/admin, sin login real para MVP).
- Dashboard: Lista productos (tabla paginada), botón "Agregar nuevo".
- Form agregar/editar: Imagen (upload simulado o URL), Título, Descripción (textarea), Precio, Categoría (select), Stock, Imagen galería (3-5).
- Acciones: Editar/Borrar. Cambios se guardan en localStorage (merge con datos mock).

## 4. Requisitos No Funcionales
- **Tech stack:** Next.js 14+ (SSR para SEO), Tailwind CSS (UI rápida/realista), localStorage/JSON para datos/carrito.
- **Datos de prueba:** Script seed en useEffect: Generar 100x5 productos con Faker.js (nombres reales como "iPhone 15 Pro", "Gucci Marmont Wallet"). Imágenes: Unsplash API o static placeholders.
- **Performance:** Lazy load imágenes, virtualización lista (react-window para 5000 items), mobile-first (PWA opcional).
- **UI/UX:** Tema luxury (negro/dorado, tipografía elegante como Playfair Display). Responsive 100% (mobile 60% tráfico).
- **Accesibilidad:** ARIA labels, keyboard nav, alt texts.
- **Seguridad:** Sanitizar inputs (XSS), no datos sensibles reales.
- **Browsers:** Chrome/FF/Safari/Edge latest + mobile.
- API services: crear una API estática con todos los productos y los services necesarios para conectarse a una API real cuando se necesite cambiar este comportamiento.

## 5. Wireframes/Flujos (Descripción)
- **Landing:** Header (logo, search, cart), Hero banner, Sidebar filtros, Main grid, Footer (categorías, contacto).
- **Carrito:** Modal overlay con lista vertical, sticky total.
- **Admin:** Sidebar nav (Productos), Form modal.
*(Recomiendo Figma para protos: landing con 48 productos visibles iniciales).*

## 6. Suposiciones y Dependencias
- Datos mock: Generados client-side (no API externa para MVP puro).
- Imágenes: URLs públicas (Unsplash) o assets locales (100+ imágenes luxury).
- Idiomas: Español (prioridad Venezuela) + English toggle.
- Deploy: Vercel/Netlify gratis.

## 7. Roadmap Post-MVP
- Backend real (Node/Supabase).
- Autenticación/Pagos Stripe.
- Wishlist/Reviews.


# PRD: Fase 2 Backend API - Las 3 Hermanas (Rails 8)

## 1. Visión General
**Objetivo:** Backend production-ready que reemplaza localStorage del MVP frontend. Soporta 100k productos, autenticación OAuth Google (client-side flow), pagos Stripe, real-time updates (websockets), bulk Excel (10k rows <10min con progreso/errores), perf optimizada (Bullet, profiling, PG indexes avanzados).

**Timeline:** 5 semanas  
**Equipo:** 2 Rails seniors  
**Métricas éxito:** 
- API <150ms p95
- Bulk success rate >98%
- 0 N+1 queries (Bullet)
- EXPLAIN ANALYZE queries <50ms

## 2. Stack Técnico Completo
```
Core: Rails 8.0 (API mode), Ruby 3.3
DB: PostgreSQL 16 + pg_trgm + unaccent
Queue: Redis 7 + Sidekiq 8 + sidekiq-bulk
Storage: ActiveStorage + S3 (prod) / Disk (dev)
Auth: Devise + Omniauth-Google-OAuth2 + JWT (knock)
Real-time: ActionCable + Redis
Search: Ransack + Elasticsearch 8 (full-text)
Payments: stripe-ruby
Perf: bullet, rack-mini-profiler, stackprof, derailed_benchmarks
Excel: roo, axlsx
Deploy: Docker + Railway/DigitalOcean
```

## 3. Database Schema y Relaciones (Migrations Completas)

```ruby
# db/schema.rb extract
ActiveRecord::Schema.define do
  # Users (OAuth Google)
  create_table "users", id: :bigint do |t|
    t.string "google_uid", null: false
    t.string "email"
    t.string "name"
    t.jsonb "avatar_url"
    t.string "jti", null: false # JWT
    t.timestamps
    t.index ["google_uid"], unique: true
    t.index ["jti"], unique: true
  end

  # Categories
  create_table "categories", id: :bigint do |t|
    t.string "name", null: false
    t.string "slug", null: false
    t.timestamps
    t.index ["slug"], unique: true
  end

  # Products (CORE)
  create_table "products", id: :bigint do |t|
    t.bigint "category_id", null: false
    t.string "title", null: false
    t.text "description"
    t.decimal "price", precision: 12, scale: 2, null: false
    t.integer "stock"
    t.float "average_rating", default: 0.0
    t.boolean "active", default: true
    t.jsonb "metadata" # {brand: "Chanel", size: "100ml"}
    t.timestamps
    
    # INDEXES AVANZADOS
    t.index ["category_id", "active", "price"], name: "idx_products_landing"
    t.index ["price", "average_rating", "created_at"], name: "idx_products_sorting"
    t.index "to_tsvector('spanish', coalesce(title,'') || ' ' || coalesce(description,''))", 
            name: "idx_products_fts_gin", using: :gin
    t.index ["title"], name: "idx_products_partial_active", 
            where: "active = true", using: :btree
  end

  # Product Images (ActiveStorage)
  create_table "active_storage_blobs", id: :bigint do |t|
    t.string "key", null: false
    t.string "filename", null: false
    t.string "content_type"
    t.text "metadata"
    t.bigint "byte_size", null: false
    t.string "checksum", null: false
    t.datetime "created_at", null: false
    t.index ["key"], unique: true
  end

  create_table "active_storage_attachments", id: :bigint do |t|
    t.string "name", null: false
    t.references "record", polymorphic: true
    t.references "blob", null: false
    t.timestamps
    t.index ["record_type", "record_id", "name", "blob_id"], name: "index_active_storage_attachments_uniqueness"
  end

  create_table "product_images", id: :bigint do |t|
    t.references "product", null: false
    t.integer "position"
    t.timestamps
  end

  # Carts & Orders
  create_table "carts", id: :bigint do |t|
    t.references "user", null: false
    t.timestamps
    t.index ["user_id"], unique: true
  end

  create_table "cart_items", id: :bigint do |t|
    t.references "cart", null: false
    t.references "product", null: false
    t.integer "quantity", default: 1
    t.timestamps
    t.index ["cart_id", "product_id"], unique: true
  end

  create_table "orders", id: :bigint do |t|
    t.references "user", null: false
    t.string "stripe_checkout_session_id"
    t.string "stripe_payment_intent_id"
    t.decimal "total_amount", precision: 12, scale: 2
    t.string "status", default: "pending" # pending/paid/failed/cancelled
    t.jsonb "billing_address"
    t.jsonb "shipping_address"
    t.timestamps
    t.index ["user_id", "status", "created_at"]
  end

  # Bulk Uploads (Sidekiq)
  create_table "bulk_uploads", id: :uuid do |t|
    t.string "status", default: "pending" # pending/processing/completed/failed
    t.bigint "user_id"
    t.string "filename"
    t.integer "total_rows", default: 0
    t.integer "processed_rows", default: 0
    t.integer "created_count", default: 0
    t.integer "updated_count", default: 0
    t.integer "error_count", default: 0
    t.jsonb "summary"
    t.timestamps
    t.index ["user_id", "status"]
  end

  create_table "bulk_upload_errors", id: :bigint do |t|
    t.references "bulk_upload", null: false, type: :uuid
    t.integer "row_number"
    t.text "error_message"
    t.jsonb "row_data"
    t.timestamps
    t.index ["bulk_upload_id", "row_number"]
  end
end
```

## 4. Model Associations Completas
```ruby
# app/models/
class User < ApplicationRecord
  devise :omniauthable, omniauth_providers: [:google_oauth2]
  has_one :cart
  has_many :orders
  has_many :bulk_uploads
end

class Product < ApplicationRecord
  belongs_to :category
  has_many :product_images, -> { order(:position) }, dependent: :destroy
  has_many :cart_items, dependent: :destroy
  has_one_attached :cover_image
  has_many_attached :gallery_images
  
  include Elasticsearch::Model
  include Elasticsearch::Model::Callbacks
  ransack_alias :search, :title_or_description_cont
end

class BulkUpload < ApplicationRecord
  belongs_to :user
  has_many :bulk_upload_errors, dependent: :destroy
  has_one_attached :spreadsheet
end
```

## 5. Endpoints API Completos (v1)

### 5.1 Auth (OAuth Google Client-Side)
```
POST   /api/v1/auth/google/callback
Params: {code: "google_auth_code"}
Response: { 
  jwt: "eyJ...", 
  user: {id: 1, email: "user@gmail.com", name: "Juan Pérez"}
}

GET    /api/v1/auth/me
Headers: Authorization: Bearer <jwt>
```

### 5.2 Landing (Públicos + Autenticados)
```
GET    /api/v1/categories
GET    /api/v1/promotions (banners)

GET    /api/v1/products
Params: 
  - ransack: category_id_eq=1&price_gte=50&active_eq=true&q=chanel
  - pagy: page=1&per_page=48
  - sort: price_asc|rating_desc|created_at_desc
Response: {
  data: [ProductSerializer],
  meta: {current_page: 1, total: 2345, pages: 49}
}

GET    /api/v1/products/:id
```

### 5.3 Admin CRUD (Autenticado)
```
# Products
POST   /api/v1/admin/products
Params: {product: {title: "...", price: 129.99, category_id: 1, metadata: {}}}
Files: cover_image, gallery_images[]

GET    /api/v1/admin/products
PUT    /api/v1/admin/products/:id
DELETE /api/v1/admin/products/:id

# Bulk
POST   /api/v1/admin/bulk_uploads
Files: spreadsheet (Excel)

GET    /api/v1/admin/bulk_uploads
GET    /api/v1/admin/bulk_uploads/:id
GET    /api/v1/admin/bulk_uploads/:id/errors?per_page=50&page=1
```

### 5.4 Carrito & Checkout
```
GET    /api/v1/cart (autenticado)
POST   /api/v1/cart_items {product_id: 123, quantity: 2}
PUT    /api/v1/cart_items/:id {quantity: 3}
DELETE /api/v1/cart_items/:id

POST   /api/v1/orders
Params: {billing_address: {...}, shipping_address: {...}}
Response: {stripe_checkout_url: "https://checkout.stripe...", order_id: 456}

POST   /api/v1/stripe/webhook (Stripe webhook)
```

## 6. Features Técnicas Detalladas

### 6.1 Sidekiq Bulk Excel Processing
```ruby
# app/jobs/bulk_upload_job.rb
class BulkUploadJob < ApplicationJob
  queue_as :bulk_uploads
  
  def perform(bulk_upload_id)
    @upload = BulkUpload.find(bulk_upload_id)
    @upload.update(status: 'processing')
    
    spreadsheet = Roo::Excel.new(@upload.spreadsheet.download)
    total_rows = spreadsheet.last_row - 1 # skip header
    
    # Process in batches of 100
    spreadsheet.each_row_streaming(offset: 2, limit: total_rows) do |row, row_index|
      process_row(row, row_index)
      @upload.increment!(:processed_rows)
    end
    
    @upload.update(status: 'completed', summary: @summary)
    Product.reindex # Elasticsearch
  end
  
  private
  
  def process_row(row, index)
    product = find_or_initialize(row)
    if product.save
      @summary[:created] += 1
    else
      BulkUploadError.create!(
        bulk_upload: @upload,
        row_number: index,
        row_data: row.to_h,
        error_message: product.errors.full_messages.join(', ')
      )
    end
  rescue => e
    BulkUploadError.create!(...)
  end
end
```

**UI Progreso Admin:**
```
BulkUploads#index → tabla: ID | Status | Processed 847/1000 | Errors 12 | Created 800
BulkUploads#show → gráfico progreso + botón "Ver Errores"
BulkUploads#errors → tabla: Row# | Data JSON | Error Message | Fix button
```

### 6.2 ActionCable Websockets
```ruby
# app/channels/
class ProductUpdatesChannel < ApplicationCable::Channel
  def subscribed
    stream_from "product_updates:#{params[:category_id]}"
  end
end

# Broadcast on product change
ProductUpdateJob.perform_later(product) # → ActionCable.server.broadcast(...)
```

**Frontend connect:** `consumer.subscriptions.create("ProductUpdatesChannel", {category_id: 1}, ...)`

### 6.3 Performance & Profiling
```
# Gemfile
gem 'bullet', group: :development
gem 'rack-mini-profiler', group: :development
gem 'stackprof', group: :development
gem 'derailed_benchmarks', group: :development

# config/environments/development.rb
config.middleware.use Rack::MiniProfiler
Bullet.enable = true
Bullet.alert = true
Bullet.bullet_logger = true

# Production profiling
GET /__stackprof__ → CPU flamegraph
```

**DB Optimization Workflow:**
1. `EXPLAIN ANALYZE SELECT * FROM products WHERE category_id=1 AND active=true ORDER BY price;`
2. Si seq_scan → agregar index compuesto
3. GIN para FTS: `to_tsvector('spanish', title||description)`
4. Partial: `WHERE active=true` (90% traffic)

### 6.4 Búsqueda Avanzada
```
# Ransack base (landing filtros)
Product.ransack(params[:q]).result(distinct: true)

# Elasticsearch full-text (q=chanel perfume)
Product.search({
  query: {multi_match: {query: "chanel perfume", fields: ["title^3", "description"]}},
  size: 48
})

# Hybrid: Ransack para filtros → ES para relevance
```

## 7. Seeds y Datos Iniciales
```ruby
# db/seeds.rb
50.times { Category.create!(name: FFaker::Product.product_name, slug: ...) }
5000.times do 
  Product.create!(title: FFaker::Product.product_name, ...)
end
Product.import # bulk insert + ES index
```

## 8. Testing Strategy
```
RSpec + FactoryBot (95% coverage)
- API: request specs todos endpoints
- Jobs: Sidekiq testing (perform_enqueued_jobs)
- Cable: ActionCable testing
- ES: elasticsearch-test-cluster
```

