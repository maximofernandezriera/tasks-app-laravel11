
# Aplicación Basic Task List en Laravel 11

Este proyecto permite a los usuarios gestionar tareas simples. Este proyecto se basa en una versión anterior donde puedes ver el resultado final: https://laravel.com/docs/5.2/quickstart

---

## **Requisitos Previos**
1. **Instalar Laravel 11**:
   Asegúrate de tener instalado PHP 8.2 o superior, Composer, y un servidor web (como Apache o Nginx). Luego, instala Laravel ejecutando:
   ```bash
   composer create-project laravel/laravel basic-task-list
   ```

2. **Configurar el Entorno**:
   Configura las variables de entorno en el archivo `.env`, como la conexión a la base de datos:
   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=basic_task_list
   DB_USERNAME=your_username
   DB_PASSWORD=your_password
   ```

   Crea la base de datos:
   ```sql
   CREATE DATABASE basic_task_list;
   ```

---

## **Paso 1: Configurar las Rutas**
Edita el archivo `routes/web.php` para definir las rutas necesarias:
```php
use App\Http\Controllers\TaskController;
use Illuminate\Support\Facades\Route;

Route::get('/', [TaskController::class, 'index'])->name('tasks.index');
Route::post('/tasks', [TaskController::class, 'store'])->name('tasks.store');
Route::delete('/tasks/{task}', [TaskController::class, 'destroy'])->name('tasks.destroy');
```

---

## **Paso 2: Crear el Modelo y la Migración**
Genera un modelo `Task` con su migración:
```bash
php artisan make:model Task -m
```

En el archivo `database/migrations/xxxx_xx_xx_create_tasks_table.php`, define la tabla `tasks`:
```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void {
        Schema::create('tasks', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    public function down(): void {
        Schema::dropIfExists('tasks');
    }
};
```

Ejecuta la migración:
```bash
php artisan migrate
```

---

## **Paso 3: Crear el Controlador**
Genera un controlador llamado `TaskController`:
```bash
php artisan make:controller TaskController
```

En `app/Http/Controllers/TaskController.php`, implementa la lógica del controlador:
```php
namespace App\Http\Controllers;

use App\Models\Task;
use Illuminate\Http\Request;

class TaskController extends Controller
{
    public function index() {
        $tasks = Task::all();
        return view('tasks.index', compact('tasks'));
    }

    public function store(Request $request) {
        $request->validate([
            'name' => 'required|max:255',
        ]);

        Task::create(['name' => $request->name]);

        return redirect()->route('tasks.index');
    }

    public function destroy(Task $task) {
        $task->delete();

        return redirect()->route('tasks.index');
    }
}
```

---

## **Paso 4: Crear las Vistas**
Crea un directorio llamado `tasks` dentro de `resources/views/` y un archivo `index.blade.php`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Task List</title>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
<body>
    <div class="container mt-5">
        <h1 class="mb-4">Basic Task List</h1>
        <!-- Add Task -->
        <form action="{{ route('tasks.store') }}" method="POST" class="mb-4">
            @csrf
            <div class="form-group">
                <input type="text" name="name" class="form-control" placeholder="Enter a new task" required>
            </div>
            <button type="submit" class="btn btn-primary mt-2">Add Task</button>
        </form>

        <!-- Current Tasks -->
        <h2>Current Tasks</h2>
        <ul class="list-group">
            @forelse ($tasks as $task)
                <li class="list-group-item d-flex justify-content-between align-items-center">
                    {{ $task->name }}
                    <form action="{{ route('tasks.destroy', $task) }}" method="POST">
                        @csrf
                        @method('DELETE')
                        <button type="submit" class="btn btn-danger btn-sm">Delete</button>
                    </form>
                </li>
            @empty
                <li class="list-group-item">No tasks yet!</li>
            @endforelse
        </ul>
    </div>
</body>
</html>
```

---

## **Paso 5: Configurar el Modelo**
En `app/Models/Task.php`, define los campos asignables:
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Task extends Model
{
    use HasFactory;

    protected $fillable = ['name'];
}
```

---

## **Paso 6: Agregar Estilos y Scripts**
Instala Laravel Vite y compila los recursos front-end. Edita `resources/css/app.css` para incluir estilos básicos:
```css
body {
    font-family: Arial, sans-serif;
    background-color: #f8f9fa;
}

.container {
    max-width: 600px;
    margin: auto;
}
```

Compila los recursos:
```bash
npm install
npm run dev
```

---

## **Paso 7: Probar la Aplicación**
Inicia el servidor local:
```bash
php artisan serve
```

Abre el navegador en [http://127.0.0.1:8000](http://127.0.0.1:8000) y prueba la aplicación.

---


### ENTREGA DE DOCUMENTACIÓN

#### Título y Descripción: Un título claro y una breve descripción del proyecto.
#### Tabla de Contenidos: Para facilitar la navegación por el documento.
####  Requisitos Previos: Herramientas y versiones necesarias para ejecutar el proyecto (como PHP, Composer, etc.).
####  Instalación: Pasos detallados para clonar el repositorio, instalar dependencias y configurar el entorno.
####  Uso: Instrucciones para ejecutar la aplicación, incluyendo comandos relevantes.
####  Testing: Cómo ejecutar el conjunto de pruebas, incluyendo comandos y ejemplos de salida.
####  Control de versiones: tal y como se espera de un proyecto "profesional" (¿ramas?)
