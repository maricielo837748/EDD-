#include <iostream>
#include <string>
using namespace std;

// Estructura del proceso
struct Proceso {
    int id;                    // Identificador único del proceso
    string nombre;             // Nombre del proceso
    int prioridad;             // Nivel de prioridad del proceso
    Proceso* siguiente;        // Puntero al siguiente proceso en la lista
};


// Lista enlazada para gestor de procesos
class ListaProcesos {
    Proceso* cabeza;           // Puntero al primer proceso de la lista
public:
    // Constructor que inicializa la lista vacía
    ListaProcesos() : cabeza(NULL) {}


        // Inserta un nuevo proceso al final de la lista
    void insertarProceso(int id, string nombre, int prioridad) {
        Proceso* nuevo = new Proceso;           // Se crea un nuevo nodo Proceso en memoria dinámica
        nuevo->id = id;                         // Se asigna el ID al nuevo proceso
        nuevo->nombre = nombre;                 // Se asigna el nombre
        nuevo->prioridad = prioridad;           // Se asigna la prioridad
        nuevo->siguiente = NULL;                // El puntero siguiente se inicializa como NULL (último elemento)
        if (!cabeza) {                          // Si la lista está vacía
            cabeza = nuevo;                     // El nuevo proceso se convierte en el primero
        } else {
            Proceso* actual = cabeza;           // Se recorre desde el principio
            while (actual->siguiente)           // Mientras no se llegue al último nodo
                actual = actual->siguiente;     // Avanza al siguiente nodo
            actual->siguiente = nuevo;          // Se enlaza el nuevo proceso al final
        }
        cout << "Proceso insertado correctamente.\n";  // Mensaje de éxito
    }


        // Muestra todos los procesos registrados en la lista
    void mostrarProcesos() {
        if (!cabeza) {                          // Si la lista está vacía
            cout << "No hay procesos registrados.\n";
            return;
        }
        Proceso* actual = cabeza;               // Se empieza desde el primer nodo
        cout << "Procesos registrados:\n";
        while (actual) {                        // Mientras haya nodos en la lista
            // Se imprime la información del proceso actual
            cout << "ID: " << actual->id << " | Nombre: " << actual->nombre << " | Prioridad: " << actual->prioridad << "\n";
            actual = actual->siguiente;         // Avanza al siguiente nodo
        }
    }


        // Busca un proceso por ID y retorna su puntero si lo encuentra
    Proceso* buscarPorID(int id) {
        Proceso* actual = cabeza;               // Se empieza desde la cabeza
        while (actual) {                        // Mientras haya procesos
            if (actual->id == id) return actual; // Si el ID coincide, retorna el puntero al proceso
            actual = actual->siguiente;         // Avanza al siguiente
        }
        return NULL;                            // Si no se encuentra, retorna NULL
    }


        // Busca un proceso por nombre y retorna su puntero si lo encuentra
    Proceso* buscarPorNombre(const string& nombre) {
        Proceso* actual = cabeza;               // Comienza desde la cabeza
        while (actual) {
            if (actual->nombre == nombre)       // Si el nombre coincide
                return actual;                  // Retorna el proceso
            actual = actual->siguiente;         // Avanza al siguiente nodo
        }
        return NULL;                            // Si no lo encuentra, retorna NULL
    }


        // Elimina un proceso de la lista por su ID
    void eliminarProceso(int id) {
        if (!cabeza) {                          // Si la lista está vacía
            cout << "No hay procesos para eliminar.\n";
            return;
        }
        if (cabeza->id == id) {                 // Si el proceso a eliminar está al principio
            Proceso* aEliminar = cabeza;        // Guarda el nodo a eliminar
            cabeza = cabeza->siguiente;         // Actualiza la cabeza al siguiente
            delete aEliminar;                   // Libera la memoria del nodo
            cout << "Proceso eliminado.\n";
            return;
        }
        Proceso* actual = cabeza;               // Nodo actual para recorrer la lista
        // Busca el nodo anterior al que se va a eliminar
        while (actual->siguiente && actual->siguiente->id != id) {
            actual = actual->siguiente;
        }
        if (actual->siguiente) {                // Si se encontró el nodo a eliminar
            Proceso* aEliminar = actual->siguiente;
            actual->siguiente = aEliminar->siguiente;  // Enlaza el anterior con el siguiente del eliminado
            delete aEliminar;                   // Libera memoria
            cout << "Proceso eliminado.\n";
        } else {
            cout << "Proceso no encontrado.\n"; // No se encontró el proceso con el ID
        }
    }


        // Modifica la prioridad de un proceso dado su ID
    void modificarPrioridad(int id, int nuevaPrioridad) {
        Proceso* p = buscarPorID(id);           // Se busca el proceso con ese ID
        if (p) {
            p->prioridad = nuevaPrioridad;      // Si se encuentra, se actualiza su prioridad
            cout << "Prioridad modificada.\n";
        } else {
            cout << "Proceso no encontrado.\n"; // Si no se encuentra, se informa
        }
    }
};
// Nodo para la cola de prioridad
struct NodoCola {
    Proceso proceso;           // El proceso almacenado en el nodo
    NodoCola* siguiente;       // Puntero al siguiente nodo en la cola
};


// Cola de prioridad (menor número = mayor prioridad)
class ColaPrioridad {
    NodoCola* frente;          // Puntero al primer nodo de la cola (mayor prioridad)
public:
    // Constructor: inicializa la cola como vacía
    ColaPrioridad() : frente(NULL) {}


        // Inserta un proceso en la cola según su prioridad
    void encolar(const Proceso& proceso) {
        NodoCola* nuevo = new NodoCola;                 // Se crea un nuevo nodo en memoria dinámica
        nuevo->proceso = proceso;                       // Se copia el proceso al nodo
        nuevo->siguiente = NULL;                        // El nodo inicialmente no apunta a ningún otro

                // Si la cola está vacía o el nuevo proceso tiene mayor prioridad (menor número)
        if (!frente || proceso.prioridad < frente->proceso.prioridad) {
            nuevo->siguiente = frente;                  // El nuevo nodo apunta al actual frente
            frente = nuevo;                             // El nuevo nodo se convierte en el frente
        } else {
            NodoCola* actual = frente;                  // Puntero auxiliar para recorrer la cola
            // Avanza mientras la prioridad del siguiente sea menor o igual (menor = mayor prioridad)
            while (actual->siguiente && actual->siguiente->proceso.prioridad <= proceso.prioridad) {
                actual = actual->siguiente;
            }
            // Inserta el nuevo nodo en la posición correcta
            nuevo->siguiente = actual->siguiente;
            actual->siguiente = nuevo;
        }
        cout << "Proceso encolado en planificador.\n";  // Mensaje de éxito
    }


        // Elimina el proceso con mayor prioridad (primer nodo de la cola)
    void desencolar() {
        if (!frente) {                                  // Si la cola está vacía
            cout << "Cola vacia.\n";
            return;
        }
        // Muestra información del proceso que será ejecutado
        cout << "Ejecutando proceso: ID=" << frente->proceso.id 
             << ", Nombre=" << frente->proceso.nombre 
             << ", Prioridad=" << frente->proceso.prioridad << "\n";
        NodoCola* aEliminar = frente;                   // Guarda el nodo a eliminar
        frente = frente->siguiente;                     // Avanza el frente al siguiente
        delete aEliminar;                               // Libera la memoria
    }


        // Muestra todos los procesos en la cola en orden de prioridad
    void mostrarCola() {
        if (!frente) {                                  // Si la cola está vacía
            cout << "Cola vacia.\n";
            return;
        }
        NodoCola* actual = frente;                      // Comienza desde el frente
        cout << "Cola de procesos por prioridad:\n";
        while (actual) {                                // Mientras haya nodos
            // Imprime información del proceso actual
            cout << "ID: " << actual->proceso.id 
                 << ", Nombre: " << actual->proceso.nombre 
                 << ", Prioridad: " << actual->proceso.prioridad << "\n";
            actual = actual->siguiente;                 // Avanza al siguiente nodo
        }
    }
};


// NODO PARA LA PILA (Gestor de memoria)
struct NodoPila {
    int bloqueMemoria;          // Representa un bloque de memoria asignado
    NodoPila* siguiente;        // Puntero al siguiente nodo en la pila (el anterior bloque asignado)
};


// Clase que representa la pila para gestionar bloques de memoria
class PilaMemoria {
    NodoPila* cima;             // Puntero al nodo que está en la cima de la pila (último bloque asignado)
public:
    // Constructor: inicializa la pila vacía
    PilaMemoria() : cima(NULL) {}


        // Asigna un nuevo bloque de memoria agregándolo a la cima de la pila
    void asignarMemoria(int bloque) {
        NodoPila* nuevo = new NodoPila;         // Crea un nuevo nodo en memoria dinámica
        nuevo->bloqueMemoria = bloque;          // Guarda el número del bloque en el nodo
        nuevo->siguiente = cima;                // El nuevo nodo apunta al que era la cima
        cima = nuevo;                           // El nuevo nodo se convierte en la nueva cima
        cout << "Memoria asignada al bloque " << bloque << "\n";  // Mensaje de éxito
    }


        // Libera el bloque de memoria en la cima de la pila
    void liberarMemoria() {
        if (!cima) {                            // Si la pila está vacía
            cout << "Pila de memoria vacia.\n"; // Mensaje de error
            return;
        }
        // Muestra el bloque que será liberado
        cout << "Memoria liberada del bloque " << cima->bloqueMemoria << "\n";
        NodoPila* aEliminar = cima;             // Guarda el nodo a eliminar
        cima = cima->siguiente;                 // La cima pasa a ser el siguiente nodo
        delete aEliminar;                       // Libera la memoria del nodo eliminado
    }


        // Muestra todos los bloques de memoria asignados (de cima a base)
    void mostrarMemoria() {
        if (!cima) {                            // Si no hay bloques asignados
            cout << "No hay memoria asignada.\n";
            return;
        }
        cout << "Estado actual de memoria (de cima a base):\n";
        NodoPila* actual = cima;                // Comienza desde la cima
        while (actual) {                        // Recorre todos los nodos de la pila
            cout << "Bloque " << actual->bloqueMemoria << "\n"; // Imprime el número del bloque
            actual = actual->siguiente;         // Avanza al siguiente bloque (más antiguo)
        }
    }
};

void mostrarMenu() {
    cout << "\n----- Sistema de Gestion de Procesos -----\n";
    cout << "1. Insertar nuevo proceso\n";
    cout << "2. Eliminar proceso\n";
    cout << "3. Buscar proceso por ID\n";
    cout << "4. Buscar proceso por nombre\n";
    cout << "5. Modificar prioridad de proceso\n";
    cout << "6. Mostrar todos los procesos\n";
    cout << "7. Encolar proceso en planificador CPU\n";
    cout << "8. Ejecutar proceso en planificador CPU\n";
    cout << "9. Mostrar cola de planificador\n";
    cout << "10. Asignar bloque de memoria\n";
    cout << "11. Liberar bloque de memoria\n";
    cout << "12. Mostrar estado de memoria\n";
    cout << "0. Salir\n";
    cout << "Seleccione una opcion: ";
}

// Verifica si una cadena contiene únicamente caracteres numéricos (dígitos del 0 al 9)
bool esNumeroPositivo(const string& s) {
    // Si la cadena está vacía, no es válida como número positivo
    if (s.empty()) return false;

    // Recorre cada carácter de la cadena
    for (size_t i = 0; i < s.length(); ++i) {
        // Si algún carácter no está entre '0' y '9', no es un número válido
        if (s[i] < '0' || s[i] > '9') return false;
    }

    // Si todos los caracteres son dígitos, la cadena es un número positivo válido
    return true;
}



// Solicita un número entero positivo al usuario, validando que la entrada sea correcta (sin usar stoi)
int pedirEnteroPositivo(const string& mensaje) {
    string entrada;  // Variable para almacenar lo que el usuario ingrese

    // Bucle infinito que solo termina cuando se ingresa un valor válido
    while (true) {
        cout << mensaje;   // Muestra el mensaje al usuario
        cin >> entrada;    // Lee la entrada como una cadena

        // Verifica si la entrada contiene solo dígitos
        if (esNumeroPositivo(entrada)) {
            int valor = 0;  // Inicializa el valor numérico en cero

            // Convierte la cadena de caracteres numéricos a un entero
            for (size_t i = 0; i < entrada.length(); ++i) {
                valor = valor * 10 + (entrada[i] - '0');  // Construye el número dígito a dígito
            }

            // Verifica que el número convertido sea mayor que cero
            if (valor > 0) return valor;  // Si es válido, lo retorna
        }

        // Si la entrada no fue válida, muestra mensaje de error y repite el bucle
        cout << "Error: Debe ingresar un numero entero positivo mayor que cero.\n";
    }
}


int main() {
    // Se crean las estructuras principales del sistema
    ListaProcesos lista;            // Lista enlazada para manejar procesos
    ColaPrioridad planificador;    // Cola de prioridad para simular el planificador de CPU
    PilaMemoria memoria;           // Pila para simular la gestión de memoria
    int opcion;                    // Variable para almacenar la opción del menú seleccionada por el usuario

    // Bucle principal del menú del sistema
    do {
        mostrarMenu();                 // Muestra el menú de opciones al usuario
        opcion = pedirEnteroPositivo("");  // Solicita al usuario una opción (entero positivo)

        // Se evalúa la opción seleccionada
        switch (opcion) {

            case 1: {  // Insertar nuevo proceso
                int id;

                // Validación para que el ID del proceso sea único
                while (true) {
                    id = pedirEnteroPositivo("Ingrese ID (entero positivo): ");
                    if (lista.buscarPorID(id) != NULL) {
                        cout << "Error: El ID ya existe. Ingrese un ID diferente.\n";
                    } else {
                        break;
                    }
                }

                // Se solicita el nombre del proceso
                cout << "Ingrese nombre del proceso: ";
                string nombre;
                cin >> nombre;

                // Se solicita la prioridad del proceso
                int prioridad = pedirEnteroPositivo("Ingrese prioridad (entero positivo): ");

                // Se inserta el nuevo proceso en la lista
                lista.insertarProceso(id, nombre, prioridad);
                break;
            }

            case 2: {  // Eliminar proceso por ID
                int id = pedirEnteroPositivo("Ingrese ID del proceso a eliminar: ");
                lista.eliminarProceso(id);
                break;
            }

            case 3: {  // Buscar proceso por ID
                int id = pedirEnteroPositivo("Ingrese ID del proceso a buscar: ");
                Proceso* p = lista.buscarPorID(id);
                if (p) {
                    cout << "Proceso encontrado: ID=" << p->id << ", Nombre=" << p->nombre << ", Prioridad=" << p->prioridad << "\n";
                } else {
                    cout << "Proceso no encontrado.\n";
                }
                break;
            }

            case 4: {  // Buscar proceso por nombre
                cout << "Ingrese nombre del proceso a buscar: ";
                string nombre;
                cin >> nombre;
                Proceso* p = lista.buscarPorNombre(nombre);
                if (p) {
                    cout << "Proceso encontrado: ID=" << p->id << ", Nombre=" << p->nombre << ", Prioridad=" << p->prioridad << "\n";
                } else {
                    cout << "Proceso no encontrado.\n";
                }
                break;
            }

            case 5: {  // Modificar prioridad de un proceso existente
                int id = pedirEnteroPositivo("Ingrese ID del proceso a modificar prioridad: ");
                int nuevaPrioridad = pedirEnteroPositivo("Ingrese nueva prioridad (entero positivo): ");
                lista.modificarPrioridad(id, nuevaPrioridad);
                break;
            }

            case 6:  // Mostrar todos los procesos de la lista
                lista.mostrarProcesos();
                break;

            case 7: {  // Encolar un proceso al planificador de CPU
                int id = pedirEnteroPositivo("Ingrese ID del proceso para encolar en planificador: ");
                Proceso* p = lista.buscarPorID(id);
                if (p) {
                    planificador.encolar(*p);  // Encola una copia del proceso encontrado
                } else {
                    cout << "Proceso no encontrado.\n";
                }
                break;
            }

            case 8:  // Ejecutar (desencolar) el proceso con mayor prioridad
                planificador.desencolar();
                break;

            case 9:  // Mostrar el estado actual de la cola de procesos
                planificador.mostrarCola();
                break;

            case 10: {  // Asignar un bloque de memoria (push en pila)
                int bloque = pedirEnteroPositivo("Ingrese identificador del bloque de memoria para asignar: ");
                memoria.asignarMemoria(bloque);
                break;
            }

            case 11:  // Liberar el último bloque de memoria asignado (pop de pila)
                memoria.liberarMemoria();
                break;

            case 12:  // Mostrar el estado actual de la memoria (contenido de la pila)
                memoria.mostrarMemoria();
                break;

            case 0:  // Salir del programa
                cout << "Saliendo...\n";
                break;

            default:  // Si se ingresa una opción inválida
                cout << "Opcion invalida.\n";
        }

    } while (opcion != 0);  // Repite el menú hasta que el usuario seleccione "Salir"

    return 0;  // Fin del programa
}
