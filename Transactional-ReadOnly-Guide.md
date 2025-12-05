# @Transactional(readOnly=true) - Cuándo y Por qué Usarlo

## ¿Qué es @Transactional(readOnly=true)?

La anotación `@Transactional(readOnly=true)` indica que una transacción es de **solo lectura**, lo que significa que no se realizarán operaciones de escritura (INSERT, UPDATE, DELETE) en la base de datos.

## Casos de Uso Principales

### 1. **Métodos de Consulta (Query Methods)**

```java
@Service
@Transactional(readOnly = true)  // A nivel de clase para todos los métodos
public class UserService {
    
    private final UserRepository userRepository;
    
    // Consultas simples
    public List<User> getAllActiveUsers() {
        return userRepository.findByActiveTrue();
    }
    
    public Optional<User> getUserById(Long id) {
        return userRepository.findById(id);
    }
    
    public List<User> getUsersByRole(String role) {
        return userRepository.findByRole(role);
    }
    
    // Consultas con JOIN y agregaciones
    public List<UserSummaryDTO> getUserSummaries() {
        return userRepository.findUserSummariesWithOrderCount();
    }
    
    // Búsquedas con paginación
    public Page<User> searchUsers(String searchTerm, Pageable pageable) {
        return userRepository.findByUsernameContainingOrEmailContaining(
            searchTerm, searchTerm, pageable);
    }
}
```

### 2. **Servicios de Reporting y Analytics**

```java
@Service
@Transactional(readOnly = true)
public class ReportingService {
    
    private final OrderRepository orderRepository;
    private final UserRepository userRepository;
    
    public SalesReportDTO generateSalesReport(LocalDate from, LocalDate to) {
        List<Order> orders = orderRepository.findByDateBetween(from, to);
        
        BigDecimal totalSales = orders.stream()
            .map(Order::getTotalAmount)
            .reduce(BigDecimal.ZERO, BigDecimal::add);
        
        long orderCount = orders.size();
        long uniqueCustomers = orders.stream()
            .map(Order::getUserId)
            .distinct()
            .count();
        
        return SalesReportDTO.builder()
            .totalSales(totalSales)
            .orderCount(orderCount)
            .uniqueCustomers(uniqueCustomers)
            .period(from + " to " + to)
            .build();
    }
    
    public List<TopSellingProductDTO> getTopSellingProducts(int limit) {
        return orderRepository.findTopSellingProducts(PageRequest.of(0, limit));
    }
    
    public DashboardDTO getDashboardMetrics() {
        long totalUsers = userRepository.count();
        long totalOrders = orderRepository.count();
        BigDecimal totalRevenue = orderRepository.calculateTotalRevenue();
        
        return DashboardDTO.builder()
            .totalUsers(totalUsers)
            .totalOrders(totalOrders)
            .totalRevenue(totalRevenue)
            .generatedAt(LocalDateTime.now())
            .build();
    }
}
```

### 3. **Validaciones y Verificaciones**

```java
@Service
@Transactional(readOnly = true)
public class ValidationService {
    
    private final UserRepository userRepository;
    private final ProductRepository productRepository;
    
    public boolean isUsernameAvailable(String username) {
        return !userRepository.existsByUsername(username);
    }
    
    public boolean isEmailAvailable(String email) {
        return !userRepository.existsByEmail(email);
    }
    
    public ValidationResult validateOrder(OrderRequest request) {
        ValidationResult result = new ValidationResult();
        
        // Verificar usuario existe
        if (!userRepository.existsById(request.getUserId())) {
            result.addError("Usuario no encontrado");
        }
        
        // Verificar productos existen y tienen stock
        for (OrderItemRequest item : request.getItems()) {
            Optional<Product> product = productRepository.findById(item.getProductId());
            if (product.isEmpty()) {
                result.addError("Producto no encontrado: " + item.getProductId());
            } else if (product.get().getStock() < item.getQuantity()) {
                result.addError("Stock insuficiente para producto: " + product.get().getName());
            }
        }
        
        return result;
    }
}
```

### 4. **Exportación de Datos**

```java
@Service
@Transactional(readOnly = true)
public class ExportService {
    
    private final UserRepository userRepository;
    private final OrderRepository orderRepository;
    
    public ByteArrayInputStream exportUsersToCSV() {
        List<User> users = userRepository.findAll();
        
        try (ByteArrayOutputStream out = new ByteArrayOutputStream();
             PrintWriter writer = new PrintWriter(new OutputStreamWriter(out))) {
            
            // Header
            writer.println("ID,Username,Email,Created Date,Status");
            
            // Data
            users.forEach(user -> {
                writer.printf("%d,%s,%s,%s,%s%n",
                    user.getId(),
                    user.getUsername(),
                    user.getEmail(),
                    user.getCreatedAt().format(DateTimeFormatter.ISO_DATE),
                    user.isActive() ? "Active" : "Inactive"
                );
            });
            
            writer.flush();
            return new ByteArrayInputStream(out.toByteArray());
            
        } catch (IOException e) {
            throw new RuntimeException("Error generating CSV", e);
        }
    }
    
    public ByteArrayInputStream exportOrdersToExcel(LocalDate from, LocalDate to) {
        List<Order> orders = orderRepository.findByDateBetween(from, to);
        
        // Lógica para generar Excel
        return generateExcelFile(orders);
    }
}
```

### 5. **APIs de Solo Consulta**

```java
@RestController
@RequestMapping("/api/catalog")
@Transactional(readOnly = true)  // Todos los endpoints son de solo lectura
public class CatalogController {
    
    private final ProductService productService;
    private final CategoryService categoryService;
    
    @GetMapping("/products")
    public ResponseEntity<Page<ProductDTO>> getProducts(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(required = false) String category,
            @RequestParam(required = false) String search) {
        
        Pageable pageable = PageRequest.of(page, size);
        Page<ProductDTO> products = productService.getProducts(category, search, pageable);
        
        return ResponseEntity.ok(products);
    }
    
    @GetMapping("/products/{id}")
    public ResponseEntity<ProductDetailDTO> getProductDetail(@PathVariable Long id) {
        ProductDetailDTO product = productService.getProductDetail(id);
        return ResponseEntity.ok(product);
    }
    
    @GetMapping("/categories")
    public ResponseEntity<List<CategoryDTO>> getCategories() {
        List<CategoryDTO> categories = categoryService.getAllCategories();
        return ResponseEntity.ok(categories);
    }
    
    @GetMapping("/featured")
    public ResponseEntity<List<ProductDTO>> getFeaturedProducts() {
        List<ProductDTO> featured = productService.getFeaturedProducts();
        return ResponseEntity.ok(featured);
    }
}
```

## Combinando readOnly con Métodos de Escritura

### Aplicar a Nivel de Clase y Sobrescribir Métodos Específicos

```java
@Service
@Transactional(readOnly = true)  // Por defecto, solo lectura
public class UserService {
    
    private final UserRepository userRepository;
    private final EmailService emailService;
    
    // Métodos de solo lectura (heredan readOnly=true)
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public Optional<User> getUserById(Long id) {
        return userRepository.findById(id);
    }
    
    public boolean existsByEmail(String email) {
        return userRepository.existsByEmail(email);
    }
    
    // Métodos de escritura (sobrescriben con readOnly=false)
    @Transactional  // readOnly=false por defecto
    public User createUser(CreateUserRequest request) {
        if (userRepository.existsByEmail(request.getEmail())) {
            throw new IllegalArgumentException("Email ya existe");
        }
        
        User user = User.builder()
            .username(request.getUsername())
            .email(request.getEmail())
            .firstName(request.getFirstName())
            .lastName(request.getLastName())
            .active(true)
            .createdAt(LocalDateTime.now())
            .build();
        
        User savedUser = userRepository.save(user);
        emailService.sendWelcomeEmail(savedUser);
        
        return savedUser;
    }
    
    @Transactional
    public User updateUser(Long id, UpdateUserRequest request) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("Usuario no encontrado"));
        
        user.setFirstName(request.getFirstName());
        user.setLastName(request.getLastName());
        user.setUpdatedAt(LocalDateTime.now());
        
        return userRepository.save(user);
    }
    
    @Transactional
    public void deleteUser(Long id) {
        if (!userRepository.existsById(id)) {
            throw new EntityNotFoundException("Usuario no encontrado");
        }
        userRepository.deleteById(id);
    }
}
```

## Ventajas de usar readOnly=true

### 1. **Optimización de Performance**

```java
@Service
public class OptimizedQueryService {
    
    // SIN readOnly=true - Menos eficiente
    @Transactional
    public List<User> getUsers() {
        return userRepository.findAll(); // Hibernate prepara para escritura
    }
    
    // CON readOnly=true - Más eficiente
    @Transactional(readOnly = true)
    public List<User> getUsersOptimized() {
        return userRepository.findAll(); // Hibernate optimiza para solo lectura
    }
}
```

### 2. **Prevención de Modificaciones Accidentales**

```java
@Service
@Transactional(readOnly = true)
public class ReportService {
    
    public List<UserReportDTO> generateUserReport() {
        List<User> users = userRepository.findAll();
        
        return users.stream().map(user -> {
            // ¡Esto causaría una excepción!
            // user.setLastAccess(LocalDateTime.now()); // WriteToReadOnlyDataSourceException
            
            return UserReportDTO.builder()
                .id(user.getId())
                .username(user.getUsername())
                .email(user.getEmail())
                .status(user.isActive() ? "Active" : "Inactive")
                .build();
        }).collect(Collectors.toList());
    }
}
```

### 3. **Routing a Read Replicas**

```java
// Configuración de múltiples DataSources
@Configuration
public class DatabaseConfig {
    
    @Primary
    @Bean
    public DataSource writeDataSource() {
        // Configuración para BD principal (escritura)
        return DataSourceBuilder.create()
            .url("jdbc:mysql://master-db:3306/myapp")
            .username("app_user")
            .password("password")
            .build();
    }
    
    @Bean
    public DataSource readDataSource() {
        // Configuración para BD replica (solo lectura)
        return DataSourceBuilder.create()
            .url("jdbc:mysql://read-replica:3306/myapp")
            .username("readonly_user")
            .password("password")
            .build();
    }
}

@Service
public class SmartRoutingService {
    
    // Va a la BD principal
    @Transactional
    public User saveUser(User user) {
        return userRepository.save(user);
    }
    
    // Va a la replica de solo lectura (más rápido)
    @Transactional(readOnly = true)
    public List<User> getUsers() {
        return userRepository.findAll();
    }
}
```

## Casos donde NO usar readOnly=true

### ❌ **Métodos que modifican datos**

```java
// MAL - Causará excepción
@Transactional(readOnly = true)
public User updateUserLastLogin(Long userId) {
    User user = userRepository.findById(userId).orElseThrow();
    user.setLastLogin(LocalDateTime.now()); // ¡Error!
    return userRepository.save(user); // ¡Error!
}
```

### ❌ **Consultas que llaman a métodos de escritura**

```java
// MAL - Si sendEmail modifica datos
@Transactional(readOnly = true)
public void processNewUsers() {
    List<User> newUsers = userRepository.findNewUsers();
    newUsers.forEach(user -> {
        emailService.sendWelcomeEmail(user); // Podría escribir logs en BD
    });
}
```

## Mejores Prácticas

### ✅ **A nivel de clase para servicios de consulta**

```java
@Service
@Transactional(readOnly = true)
public class QueryOnlyService {
    // Todos los métodos son de solo lectura por defecto
}
```

### ✅ **Métodos específicos en servicios mixtos**

```java
@Service
public class MixedService {
    
    @Transactional(readOnly = true)
    public List<Entity> getAll() { ... }
    
    @Transactional
    public Entity save(Entity entity) { ... }
}
```

### ✅ **En controladores de API de consulta**

```java
@RestController
@RequestMapping("/api/reports")
@Transactional(readOnly = true)
public class ReportsController {
    // Todos los endpoints son de solo lectura
}
```

**En resumen**: Usa `@Transactional(readOnly=true)` para operaciones que solo leen datos, ya que mejora el performance, previene modificaciones accidentales y permite optimizaciones de routing a bases de datos de solo lectura.
