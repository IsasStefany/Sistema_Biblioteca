
//Alumno: 
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

public class Alumno extends Usuario {
    private String carrera;
    private String codigoAlumno;
    private int ciclo;

    public Alumno(String idUsuario, String nombre, String email, String telefono, String password, String carrera, String codigoAlumno, int ciclo) {
        super(idUsuario, nombre, email, telefono, password);
        this.carrera = carrera;
        this.codigoAlumno = codigoAlumno;
        this.ciclo = ciclo;
    }
    
    
    @Override
    public boolean reservarRecurso(int recursoId, Biblioteca biblioteca) {
        Recurso r = biblioteca.buscarRecursoPorId(recursoId);
        if (r instanceof Reservable) {
            return ((Reservable) r).reservar(this.nombre, LocalDate.now(), LocalDate.now().plusDays(1));
        }
        return false;
    }

    @Override
    public List<Prestamo> verHistorialPrestamos(Biblioteca biblioteca) {
        return biblioteca.consultarPrestamosPorUsuario(this.idUsuario);
    }

    @Override
    public String toString() {
        return "Alumno " + nombre + " (" + codigoAlumno + ")";
    }
}

Ambiente
import java.time.LocalDate;

public abstract class Ambiente extends RecursoFisico {
    protected int capacidad;
    public Ambiente(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, int capacidad) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.capacidad = capacidad;
    }
    public int getCapacidad() { return capacidad; }

    @Override
    public String consultarDetalles() {
        return String.format("Ambiente[id=%d,titulo=%s,capacidad=%d]", id, titulo, capacidad);
    }
}

Biblioteca
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

public class Biblioteca {
    private int idBiblioteca;
    private String nombre;
    private String direccion;

    // Agregaciones
    private List<Usuario> usuarios = new ArrayList<>();
    private List<Recurso> recursos = new ArrayList<>();
    private List<Bibliotecario> bibliotecarios = new ArrayList<>();
    private List<Prestamo> prestamos = new ArrayList<>();

    public Biblioteca(int idBiblioteca, String nombre, String direccion) {
        this.idBiblioteca = idBiblioteca;
        this.nombre = nombre;
        this.direccion = direccion;
    }

    // Usuarios
    public void registrarUsuario(Usuario u) { usuarios.add(u); }
    public boolean eliminarUsuario(String idUsuario) { return usuarios.removeIf(u -> u.getIdUsuario() == idUsuario); }
    public List<Usuario> consultarUsuarios() { return new ArrayList<>(usuarios); }

    // Recursos
    public void registrarRecurso(Recurso r) { recursos.add(r); }
    public boolean eliminarRecurso(int recursoId) { return recursos.removeIf(r -> r.getId() == recursoId); }
    public List<Recurso> consultarRecursos() { return new ArrayList<>(recursos); }
    public void actualizarRecurso(Recurso r) {
    for (int i = 0; i < recursos.size(); i++) {
        if (recursos.get(i).getId() == r.getId()) {    // comparar enteros
            recursos.set(i, r);
            System.out.println("Recurso actualizado correctamente: id=" + r.getId());
            return;
        }
    }
    System.out.println("Recurso no encontrado para actualizar. id=" + r.getId());
}

    // Bibliotecarios
    public void asignarBibliotecario(Bibliotecario b) {
        bibliotecarios.add(b);
        b.setBiblioteca(this);
    }
    public List<Bibliotecario> consultarBibliotecarios() { return new ArrayList<>(bibliotecarios); }

   

    public List<Prestamo> consultarPrestamos() { return new ArrayList<>(prestamos); }

    public List<Prestamo> consultarPrestamosPorUsuario(String idUsuario) {
        return prestamos.stream().filter(p -> p.getUsuario().getIdUsuario() == idUsuario).collect(Collectors.toList());
    }

    public Recurso buscarRecursoPorId(int id) {
        return recursos.stream().filter(r -> r.getId() == id).findFirst().orElse(null);
    }
    // ===============================
// Métodos para mostrar información en consola
// ===============================
public void mostrarUsuarios() {
    System.out.println("\n--- Lista de Usuarios ---");
    if (usuarios.isEmpty()) {
        System.out.println("No hay usuarios registrados.");
    } else {
        for (Usuario u : usuarios) {
            System.out.println("ID: " + u.getIdUsuario() + " | Nombre: " + u.getNombre());
        }
    }
}

public void mostrarRecursos() {
    System.out.println("\n--- Lista de Recursos ---");
    if (recursos.isEmpty()) {
        System.out.println("No hay recursos registrados.");
    } else {
        for (Recurso r : recursos) {
            System.out.println(r.consultarDetalles());
        }
    }
}

public void mostrarPrestamos() {
    System.out.println("\n--- Lista de Préstamos ---");
    if (prestamos.isEmpty()) {
        System.out.println("No hay préstamos registrados.");
    } else {
        for (Prestamo p : prestamos) {
            System.out.println("Usuario: " + p.getUsuario().getNombre() + 
                               " | Recurso: " + p.getRecurso().getTitulo() + 
                               " | Fecha inicio: " + p.getFechaInicio() + 
                               " | Fecha fin: " + p.getFechaDevolucion());
        }
    }
}

}
Biblotecario
import java.time.LocalDate;
import java.util.List;

public class Bibliotecario extends Usuario {
    private int idEmpleado;
    private String turno;
    private String especialidad;
    private Biblioteca biblioteca; // referencia bidireccional

    public Bibliotecario(String id, String nombre, String email, String telefono, String password, int idEmpleado, String turno, String especialidad) {
        super(id, nombre, email, telefono, password);
        this.idEmpleado = idEmpleado;
        this.turno = turno;
        this.especialidad = especialidad;
    }

    public void setBiblioteca(Biblioteca biblioteca) { this.biblioteca = biblioteca; }
    public Biblioteca getBiblioteca() { return biblioteca; }

    // Métodos administrativos
    public void registrarRecurso(Recurso r) { if (biblioteca != null) biblioteca.registrarRecurso(r); }
    public void actualizarRecurso(Recurso r) { if (biblioteca != null) biblioteca.actualizarRecurso(r); }
    public void eliminarRecurso(int recursoId) { if (biblioteca != null) biblioteca.eliminarRecurso(recursoId); }

    public Prestamo registrarPrestamo(Usuario u, Recurso r) {
        if (biblioteca == null) return null;
        return biblioteca.registrarPrestamo(u, r);
    }

    public void aplicarPenalizacion(Prestamo p) {
        Penalizacion pen = p.generarPenalizacion();
        if (pen != null) {
            System.out.println("Aplicando penalización: " + pen.calcularMonto());
        }
    }

    @Override
    public boolean reservarRecurso(int recursoId, Biblioteca biblioteca) {
        // El bibliotecario puede reservar cualquier recurso para mantenimiento (por ejemplo)
        Recurso r = biblioteca.buscarRecursoPorId(recursoId);
        if (r instanceof Reservable) {
            return ((Reservable) r).reservar(this.nombre, LocalDate.now(), LocalDate.now());
        }
        return false;
    }

    @Override
    public java.util.List<Prestamo> verHistorialPrestamos(Biblioteca biblioteca) {
        return biblioteca.consultarPrestamosPorUsuario(this.idUsuario);
    }

    @Override
    public String toString() {
        return "Bibliotecario " + nombre + " (Empleado " + idEmpleado + ")";
    }
}

Invitado
import java.time.LocalDate;
import java.util.List;

public class Invitado extends Usuario {
    private String tipoInvitado;
    private java.time.LocalDate fechaExpiracion;

    public Invitado(String id, String nombre, String email, String telefono, String password, String tipoInvitado, java.time.LocalDate fechaExpiracion) {
        super(id, nombre, email, telefono, password);
        this.tipoInvitado = tipoInvitado;
        this.fechaExpiracion = fechaExpiracion;
    }

    @Override
    public boolean reservarRecurso(int recursoId, Biblioteca biblioteca) {
        // Invitados solo pueden reservar ambientes (por ejemplo)
        Recurso r = biblioteca.buscarRecursoPorId(recursoId);
        if (r instanceof Ambiente) {
            return ((Reservable) r).reservar(this.nombre, LocalDate.now(), LocalDate.now());
        }
        return false;
    }

    @Override
    public List<Prestamo> verHistorialPrestamos(Biblioteca biblioteca) {
        return biblioteca.consultarPrestamosPorUsuario(this.idUsuario);
    }

    @Override
    public String toString() {
        return "Invitado " + nombre + " (" + tipoInvitado + ")";
    }
}

Profesor
import java.time.LocalDate;
import java.util.List;

public class Profesor extends Usuario {
    private String facultad;
    private String especialidad;
    private String codigoProfesor;

    public Profesor(String id, String nombre, String email, String telefono, String password, String facultad, String especialidad, String codigoProfesor) {
        super(id, nombre, email, telefono, password);
        this.facultad = facultad;
        this.especialidad = especialidad;
        this.codigoProfesor = codigoProfesor;
    }

    @Override
    public boolean reservarRecurso(int recursoId, Biblioteca biblioteca) {
        Recurso r = biblioteca.buscarRecursoPorId(recursoId);
        if (r instanceof Reservable) {
            return ((Reservable) r).reservar(this.nombre, LocalDate.now(), LocalDate.now().plusDays(2));
        }
        return false;
    }

    @Override
    public java.util.List<Prestamo> verHistorialPrestamos(Biblioteca biblioteca) {
        return biblioteca.consultarPrestamosPorUsuario(this.idUsuario);
    }

    @Override
    public String toString() {
        return "Profesor " + nombre + " (" + codigoProfesor + ")";
    }
}


SalaEstudio
import java.time.LocalDate;

public class SalaEstudio extends Ambiente {
    private int numMesas;

    public SalaEstudio(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, int capacidad, int numMesas) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, capacidad);
        this.numMesas = numMesas;
    }

    public boolean consultarDisponibilidadMesa() { return disponible; }

    @Override
    public String consultarDetalles() {
        return String.format("SalaEstudio[id=%d,titulo=%s,capacidad=%d,mesas=%d]", id, titulo, capacidad, numMesas);
    }
}
SalaLectura
import java.time.LocalDate;

public class SalaLectura extends Ambiente {
    private int numSillas;

    public SalaLectura(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, int capacidad, int numSillas) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, capacidad);
        this.numSillas = numSillas;
    }

    public int consultarCapacidad() { return capacidad; }

    @Override
    public String consultarDetalles() {
        return String.format("SalaLectura[id=%d,titulo=%s,capacidad=%d,sillas=%d]", id, titulo, capacidad, numSillas);
    }
}


Usuario
import java.util.List;

public abstract class Usuario {
    protected String idUsuario;
    protected String nombre;
    protected String email;
    protected String telefono;
    protected String password;

    public Usuario(String idUsuario, String nombre, String email, String telefono, String password) {
        this.idUsuario = idUsuario;
        this.nombre = nombre;
        this.email = email;
        this.telefono = telefono;
        this.password = password;
    }

    public String getIdUsuario() { return idUsuario; }
    public String getNombre() { return nombre; }
    public String getEmail() { return email; }

    // Acciones delegadas a Prestamo/Biblioteca
    public abstract boolean reservarRecurso(int recursoId, Biblioteca biblioteca);
    public abstract java.util.List<Prestamo> verHistorialPrestamos(Biblioteca biblioteca);

    @Override
    public String toString() {
        return String.format("Usuario[id=%d,nombre=%s,email=%s]", idUsuario, nombre, email);
    }
}
MAIN
import java.time.LocalDate;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Biblioteca biblioteca = new Biblioteca(01,"UTP Central","Av. Arequipa");
        int opcionPrincipal;

        do {
            System.out.println("\n===== SISTEMA DE GESTIÓN DE BIBLIOTECA =====");
            System.out.println("1. Gestión de Usuarios");
            System.out.println("2. Gestión de Recursos");
            System.out.println("3. Gestión de Préstamos");
            System.out.println("4. Mostrar todos los datos");
            System.out.println("5. Salir");
            System.out.print("Seleccione una opción: ");
            opcionPrincipal = sc.nextInt();
            sc.nextLine(); // limpiar buffer

            switch (opcionPrincipal) {
                case 1 -> menuUsuarios(sc, biblioteca);
                case 2 -> menuRecursos(sc, biblioteca);
                case 3 -> menuPrestamos(sc, biblioteca);
                case 4 -> {
                    biblioteca.consultarUsuarios();
                    biblioteca.mostrarRecursos();
                    biblioteca.mostrarPrestamos();
                }
                case 5 -> System.out.println("👋 Saliendo del sistema...");
                default -> System.out.println("❌ Opción inválida. Intente nuevamente.");
            }
        } while (opcionPrincipal != 5);

        sc.close();
    }

    // -------------------- SUBMENÚ USUARIOS --------------------
    private static void menuUsuarios(Scanner sc, Biblioteca biblioteca) {
        int opcion;
        do {
            System.out.println("\n--- GESTIÓN DE USUARIOS ---");
            System.out.println("1. Registrar usuario");
            System.out.println("2. Mostrar usuarios");
            System.out.println("3. Volver al menú principal");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine();

            switch (opcion) {
                case 1 -> {
                    System.out.print("Id Usuario: ");
                    String idUsuario = sc.nextLine();
                    
                    System.out.print("Nombre: ");
                    String nombre = sc.nextLine();
                    
                    System.out.print("Email: ");
                    String email = sc.nextLine();
                    
                    System.out.print("Telefono: ");
                    String telefono = sc.nextLine();
                    
                    System.out.print("Password: ");
                    String password = sc.nextLine();
                    
                    System.out.print("Carrera: ");
                    String carrera = sc.nextLine();
                    
                    System.out.print("Codigo de Alumno: ");
                    String codigoAlumno = sc.nextLine();
                    
                    System.out.print("Ciclo: ");
                    int ciclo = sc.nextInt();
                    
                    sc.nextLine();

                    Usuario usuario = new Alumno(idUsuario, nombre,  email,  telefono, password, carrera, codigoAlumno, ciclo);
                    biblioteca.registrarUsuario(usuario);
                }
                case 2 -> biblioteca.mostrarUsuarios();
                case 3 -> System.out.println("Volviendo al menú principal...");
                default -> System.out.println("Opción inválida.");
            }
        } while (opcion != 3);
    }

    // -------------------- SUBMENÚ RECURSOS --------------------
    private static void menuRecursos(Scanner sc, Biblioteca biblioteca) {
        int opcion;
        do {
            System.out.println("\n--- GESTIÓN DE RECURSOS ---");
            System.out.println("1. Registrar recurso");
            System.out.println("2. Mostrar recursos");
            System.out.println("3. Eliminar recurso");
            System.out.println("4. Volver al menú principal");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine();

            switch (opcion) {
                case 1 -> {
                System.out.print("ID del recurso: ");
                int id = sc.nextInt();
                sc.nextLine(); // limpiar buffer

                System.out.print("Título: ");
                String titulo = sc.nextLine();

                System.out.print("Ubicación: ");
                String ubicacion = sc.nextLine();

                System.out.print("Condición: ");
                String condicion = sc.nextLine();

                System.out.print("Autor: ");
                String autor = sc.nextLine();

                System.out.print("Número de páginas: ");
                int numPaginas = sc.nextInt();
                sc.nextLine(); // limpiar buffer

                // Crear el objeto correctamente con todos los parámetros
                Recurso r = new LibroFisico(id, titulo, LocalDate.now(), ubicacion, condicion, autor, numPaginas);

                biblioteca.registrarRecurso(r);
                System.out.println("✅ Libro registrado exitosamente.");
            }

                case 2 -> biblioteca.mostrarRecursos();
                case 3 -> {
                    System.out.print("Ingrese ID del recurso a eliminar: ");
                    int id = sc.nextInt();
                    sc.nextLine();
                    biblioteca.eliminarRecurso(id);
                }
                case 4 -> System.out.println("Volviendo al menú principal...");
                default -> System.out.println("Opción inválida.");
            }
        } while (opcion != 4);
    }

    // -------------------- SUBMENÚ PRÉSTAMOS --------------------
    private static void menuPrestamos(Scanner sc, Biblioteca biblioteca) {
        int opcion;
        do {
            System.out.println("\n--- GESTIÓN DE PRÉSTAMOS ---");
            System.out.println("1. Registrar préstamo");
            System.out.println("2. Mostrar préstamos");
            System.out.println("3. Volver al menú principal");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine();

            switch (opcion) {
                case 1 -> {
                    

                    System.out.print("ID del usuario: ");
                    String idUsuario = sc.nextLine();
                    
                    sc.nextLine();
                    System.out.print("Nombre: ");
                    String nombre = sc.nextLine();
                    
                    System.out.print("Email: ");
                    String email = sc.nextLine();
                    
                    System.out.print("Telefono: ");
                    String telefono = sc.nextLine();
                    
                    System.out.print("Password: ");
                    String password = sc.nextLine();
                    
                    System.out.print("Carrera: ");
                    String carrera = sc.nextLine();
                    
                    System.out.print("Codigo de Alumno: ");
                    String codigoAlumno = sc.nextLine();
                    
                    System.out.print("Ciclo: ");
                    int ciclo = sc.nextInt();
                    
                    System.out.print("ID del recurso: ");
                    int idRecurso = sc.nextInt();
                    
                    System.out.print("Título: ");
                    String titulo = sc.nextLine();
                    sc.nextLine();
                    System.out.print("Autor: ");
                    String autor = sc.nextLine();

                    System.out.print("Ubicación: ");
                    String ubicacion = sc.nextLine();

                    System.out.print("Condición: ");
                    String condicion = sc.nextLine();
                  

                    System.out.print("Número de páginas: ");
                    int numPaginas = sc.nextInt();                    
            
                    sc.nextLine(); // limpiar buffer
                    
                    Usuario u = new Alumno(idUsuario, nombre,  email,  telefono, password, carrera, codigoAlumno, ciclo);
                    Recurso r = new LibroFisico(idRecurso, titulo, LocalDate.now(), ubicacion, condicion, autor, numPaginas);
                    Prestamo p = new Prestamo(u,r, LocalDate.now(), LocalDate.now().plusDays(7));

                    biblioteca.registrarPrestamo(u,r);
                }
                case 2 -> biblioteca.mostrarPrestamos();
                case 3 -> System.out.println("Volviendo al menú principal...");
                default -> System.out.println("Opción inválida.");
            }
        } while (opcion != 3);
    }
}
