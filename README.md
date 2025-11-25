package sgbu.model;

import java.time.LocalDate;

public class ArticuloDigital extends RecursoDigital {
    private String revistaOrigen;
    private String doi;

    public ArticuloDigital(int id, String titulo, LocalDate fechaIngreso,
                           String formato, String urlAcceso, String revistaOrigen, String doi) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.revistaOrigen = revistaOrigen;
        this.doi = doi;
    }

    public String generarReferencia() {
        return revistaOrigen + " - DOI: " + doi;
    }

    @Override
    public String consultarDetalles() {
        return String.format("ArticuloDigital[id=%d, titulo=%s, revista=%s, doi=%s]", id, titulo, revistaOrigen, doi);
    }
    public String getRevistaOrigen() {
    return revistaOrigen;
}

    public String getDoi() {
    return doi;
}

}
package sgbu.model;

import java.time.LocalDate;

public class ArticuloDigital extends RecursoDigital {
    private String revistaOrigen;
    private String doi;

    public ArticuloDigital(int id, String titulo, LocalDate fechaIngreso,
                           String formato, String urlAcceso, String revistaOrigen, String doi) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.revistaOrigen = revistaOrigen;
        this.doi = doi;
    }

    public String generarReferencia() {
        return revistaOrigen + " - DOI: " + doi;
    }

    @Override
    public String consultarDetalles() {
        return String.format("ArticuloDigital[id=%d, titulo=%s, revista=%s, doi=%s]", id, titulo, revistaOrigen, doi);
    }
    public String getRevistaOrigen() {
    return revistaOrigen;
}

    public String getDoi() {
    return doi;
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

    // Prestamos
    public Prestamo registrarPrestamo(Usuario usuario, Recurso recurso) {
        if (!recurso.isDisponible()) {
            System.out.println("Recurso no disponible: " + recurso.getTitulo());
            return null;
        }
        LocalDate inicio = LocalDate.now();
        LocalDate fin = inicio.plusDays(7); // periodo por defecto
        Prestamo p = new Prestamo(usuario, recurso, inicio, fin);
        recurso.setDisponible(false);
        prestamos.add(p);
        System.out.println("Prestamo registrado: " + p);
        return p;
    }

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
package sgbu.model;

import java.time.LocalDate;
import java.util.List;

public class Bibliotecario extends Usuario {
    private int idEmpleado;
    private String turno;
    private String especialidad;
    private Biblioteca biblioteca;

    public Bibliotecario(String id, String nombre, String email, String telefono, String password,
                         int idEmpleado, String turno, String especialidad) {
        super(id, nombre, email, telefono, password);
        this.idEmpleado = idEmpleado;
        this.turno = turno;
        this.especialidad = especialidad;
    }

    public void setBiblioteca(Biblioteca biblioteca) { this.biblioteca = biblioteca; }

    public void registrarRecurso(Recurso r) { if (biblioteca != null) biblioteca.registrarRecurso(r); }

    public void eliminarRecurso(int recursoId) { if (biblioteca != null) biblioteca.eliminarRecurso(recursoId); }

    public Prestamo registrarPrestamo(Usuario u, Recurso r) {
        if (biblioteca == null) return null;
        return biblioteca.registrarPrestamo(u, r);
    }

    public void aplicarPenalizacion(Prestamo p) {
        Penalizacion pen = p.generarPenalizacion();
        if (pen != null) {
            System.out.println("Penalización aplicada: S/. " + pen.calcularMonto());
        }
    }

    @Override
    public boolean reservarRecurso(int recursoId, Biblioteca biblioteca) {
        Recurso r = biblioteca.buscarRecursoPorId(recursoId);
        if (r instanceof Reservable reservable) {
            return reservable.reservar(this.nombre, LocalDate.now(), LocalDate.now().plusDays(1));
        }
        return false;
    }

    @Override
    public List<Prestamo> verHistorialPrestamos(Biblioteca biblioteca) {
        return biblioteca.consultarPrestamosPorUsuario(this.idUsuario);
    }

    @Override
    public String toString() {
        return "Bibliotecario " + nombre + " (ID empleado: " + idEmpleado + ")";
    }
    public int getIdEmpleado() {
    return idEmpleado;
}

    public String getTurno() {
    return turno;
}

    public String getEspecialidad() {
    return especialidad;
}

}

package sgbu.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DBConnection {
    private static final String URL =
        "jdbc:sqlserver://localhost:1433;databaseName=DB_Biblioteca;encrypt=false;integratedSecurity=true;";

    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(URL);
        } catch (SQLException e) {
            System.out.println("❌ Error al conectar: " + e.getMessage());
            return null;
        }
    }
}

import java.time.LocalDate;

public class Devolucion {

    private int idDevolucion;
    private int idPrestamo;
    private LocalDate fechaDevolucion;
    private String estado; // "devuelto", "retraso", etc.
    private double penalizacion;  // ⬅️ Nuevo campo
    private String idUsuario;
    public Devolucion(int idDevolucion, int idPrestamo, LocalDate fechaDevolucion, String estado) {
        this.idDevolucion = idDevolucion;
        this.idPrestamo = idPrestamo;
        this.fechaDevolucion = fechaDevolucion;
        this.estado = estado;
    }

    public Devolucion(int idPrestamo, LocalDate fechaDevolucion, String estado) {
        this.idPrestamo = idPrestamo;
        this.fechaDevolucion = fechaDevolucion;
        this.estado = estado;
    }

    public int getIdDevolucion() {
        return idDevolucion;
    }

    public void setIdDevolucion(int idDevolucion) {
        this.idDevolucion = idDevolucion;
    }

    public int getIdPrestamo() {
        return idPrestamo;
    }

    public void setIdPrestamo(int idPrestamo) {
        this.idPrestamo = idPrestamo;
    }

    public LocalDate getFechaDevolucion() {
        return fechaDevolucion;
    }

    public void setFechaDevolucion(LocalDate fechaDevolucion) {
        this.fechaDevolucion = fechaDevolucion;
    }

    public String getEstado() {
        return estado;
    }

    public void setEstado(String estado) {
        this.estado = estado;
    }
    public double getPenalizacion() {
        return penalizacion;
    }

    public void setPenalizacion(double penalizacion) {
        this.penalizacion = penalizacion;
    }
    public String getIdUsuario() {
    return idUsuario;
    }

    public void setIdUsuario(String idUsuario) {
    this.idUsuario = idUsuario;
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
package sgbu.model;

import java.time.LocalDate;

public class LibroDigital extends RecursoDigital {
    private String autor;
    private int numPaginas;

    public LibroDigital(int id, String titulo, LocalDate fechaIngreso,
                        String formato, String urlAcceso, String autor, int numPaginas) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.autor = autor;
        this.numPaginas = numPaginas;
    }

    public String generarCitaAPA() {
        return autor + ". (" + fechaIngreso.getYear() + "). " + titulo + ".";
    }

    @Override
    public String consultarDetalles() {
        return String.format("LibroDigital[id=%d, titulo=%s, autor=%s, formato=%s]", id, titulo, autor, formato);
    }
    public String getAutor() {
    return autor;
}

    public int getNumPaginas() {
    return numPaginas;
}

}

package sgbu.model;

public class Penalizacion {
    private static final double TARIFA_POR_DIA = 1.5;
    private int diasRetraso;

    public Penalizacion(int diasRetraso) {
        this.diasRetraso = diasRetraso;
    }

    public int getDiasRetraso() {
        return diasRetraso;
    }

    public double calcularMonto() {
        return diasRetraso * TARIFA_POR_DIA;
    }

    public void notificarUsuario(Usuario u) {
        System.out.println("Notificando a " + u.getNombre() + " sobre penalización de S/. " + calcularMonto());
    }
}
package sgbu.model;

import java.time.LocalDate;
import java.time.temporal.ChronoUnit;

public class Prestamo {
    private static int contador = 1;

    private int idPrestamo;
    private Usuario usuario;
    private Recurso recurso;
    private LocalDate fechaInicio;
    private LocalDate fechaDevolucion;
    private String estado;
    private Penalizacion penalizacion;

    public Prestamo(Usuario usuario, Recurso recurso, LocalDate inicio, LocalDate fin) {
        this.idPrestamo = contador++;
        this.usuario = usuario;
        this.recurso = recurso;
        this.fechaInicio = inicio;
        this.fechaDevolucion = fin;
        this.estado = "vigente";
    }

    public int getIdPrestamo() { return idPrestamo; }
    public Usuario getUsuario() { return usuario; }
    public Recurso getRecurso() { return recurso; }
    public LocalDate getFechaInicio() { return fechaInicio; }
    public LocalDate getFechaDevolucion() { return fechaDevolucion; }
    public String getEstado() { return estado; }

    public void setIdPrestamo(int id) { this.idPrestamo = id; }
    public void setEstado(String estado) { this.estado = estado; }

    public void devolverRecurso() {
        LocalDate hoy = LocalDate.now();
        if (hoy.isAfter(fechaDevolucion)) {
            estado = "atrasado";
            this.penalizacion = generarPenalizacion();
        } else {
            estado = "devuelto";
        }
        recurso.setDisponible(true);
    }

    public Penalizacion generarPenalizacion() {
        long dias = ChronoUnit.DAYS.between(fechaDevolucion, LocalDate.now());
        if (dias > 0) {
            return new Penalizacion((int) dias);
        }
        return null;
    }

    @Override
    public String toString() {
        return String.format("Prestamo[id=%d, usuario=%s, recurso=%s, estado=%s]",
                idPrestamo, usuario.getNombre(), recurso.getTitulo(), estado);
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
package sgbu.model;

import java.time.LocalDate;

public abstract class RecursoDigital extends Recurso {
    protected String formato;
    protected String urlAcceso;

    public RecursoDigital(int id, String titulo, LocalDate fechaIngreso, String formato, String urlAcceso) {
        super(id, titulo, fechaIngreso);
        this.formato = formato;
        this.urlAcceso = urlAcceso;
    }

    public String getFormato() { return formato; }
    public String getUrlAcceso() { return urlAcceso; }

    public void visualizarOnline() {
        System.out.println("Abriendo visor para: " + titulo + " en " + urlAcceso);
    }

    public void descargar() {
        System.out.println("Descargando: " + titulo + " formato " + formato);
    }

    @Override
    public String consultarDetalles() {
        return String.format("RecursoDigital[id=%d, titulo=%s, formato=%s, url=%s]", id, titulo, formato, urlAcceso);
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




Recurso
import java.time.LocalDate;

public abstract class Recurso {
    protected int id;
    protected String titulo;
    protected boolean disponible;
    protected LocalDate fechaIngreso;

    public Recurso(int id, String titulo, LocalDate fechaIngreso) {
        this.id = id;
        this.titulo = titulo;
        this.disponible = true;
        this.fechaIngreso = fechaIngreso;
    }

    public int getId() { return id; }
    public String getTitulo() { return titulo; }
    public boolean isDisponible() { return disponible; }
    public LocalDate getFechaIngreso() { return fechaIngreso; }

    public void setDisponible(boolean disponible) { this.disponible = disponible; }

    // Información genérica del recurso
    public String consultarDetalles() {
        return String.format("Recurso[id=%d, titulo=%s, disponible=%b]", id, titulo, disponible);
    }
}


RecursoFisico
import java.time.LocalDate;

public abstract class RecursoFisico extends Recurso implements Reservable {
    protected String ubicacionEstante;
    protected String condicion;
    private boolean reservado = false;

    public RecursoFisico(int id, String titulo, LocalDate fechaIngreso, String ubicacionEstante, String condicion) {
        super(id, titulo, fechaIngreso);
        this.ubicacionEstante = ubicacionEstante;
        this.condicion = condicion;
    }

    public String getUbicacionEstante() { return ubicacionEstante; }
    public String getCondicion() { return condicion; }

    @Override
    public boolean reservar(String quien, LocalDate fechaInicio, LocalDate fechaFin) {
        if (!disponible || reservado) return false;
        reservado = true;
        return true;
    }

    @Override
    public void liberar() { reservado = false; }

    @Override
    public boolean estaReservado() { return reservado; }

    @Override
    public String consultarDetalles() {
        return String.format("Fisico[id=%d,titulo=%s,estante=%s,cond=%s,disp=%b]", id, titulo, ubicacionEstante, condicion, disponible);
    }
}
package sgbu.ui;

import sgbu.dao.*;
import sgbu.model.*;
import sgbu.util.ArchivoUtil;

import java.time.LocalDate;
import java.util.List;
import java.util.Scanner;

public class SistemaBiblioteca {
    private static final Scanner sc = new Scanner(System.in);
    private static final Biblioteca biblioteca = new Biblioteca(1, "Central", "Av. Principal 123");

    private static final UsuarioDAO usuarioDAO = new UsuarioDAO();
    private static final RecursoDAO recursoDAO = new RecursoDAO();
    private static final PrestamoDAO prestamoDAO = new PrestamoDAO();
    private static final DevolucionDAO devolucionDAO = new DevolucionDAO();

    public static void main(String[] args) {
        cargarDatosIniciales();

        while (true) {
            System.out.println("\n=== 🔐 Sistema de Biblioteca - Login ===");
            System.out.print("ID Bibliotecario: ");
            String id = sc.nextLine();

            System.out.print("Password: ");
            String password = sc.nextLine();

            Usuario usuario = biblioteca.buscarUsuarioPorId(id);

            if (usuario instanceof Bibliotecario biblio && biblio.getPassword().equals(password)) {
                System.out.println("✅ Bienvenido bibliotecario: " + biblio.getNombre());
                mostrarMenuBibliotecario();
            } else {
                System.out.println("❌ Credenciales incorrectas o usuario no es bibliotecario.");
            }
        }
    }

    private static void cargarDatosIniciales() {
        // Cargar usuarios desde archivo (opcional)
        // Cargar usuarios desde base de datos
        UsuarioDAO usuarioDAO = new UsuarioDAO();
        List<Usuario> listaUsuarios = usuarioDAO.listarUsuarios();
        for (Usuario u : listaUsuarios) {
        biblioteca.registrarUsuario(u);
}
        System.out.println("? Usuarios cargados en memoria: " + listaUsuarios.size());


        // Cargar recursos desde BD
        List<Recurso> recursosBD = recursoDAO.listarRecursos();
        for (Recurso r : recursosBD) {
            biblioteca.registrarRecurso(r);
        }

        // Cargar préstamos desde BD
        List<Prestamo> prestamosBD = prestamoDAO.obtenerPrestamos(biblioteca);
        for (Prestamo p : prestamosBD) {
            biblioteca.registrarPrestamoDesdeBD(p);
        }

        System.out.println("✅ Datos iniciales cargados desde la base de datos.");
    }

    private static void mostrarMenuBibliotecario() {
        int opcion;
        do {
            System.out.println("\n📚 === Menú Principal ===");
            System.out.println("1. Gestión de Usuarios");
            System.out.println("2. Gestión de Recursos");
            System.out.println("3. Préstamos y Devoluciones");
            System.out.println("4. Consultas / Reportes");
            System.out.println("0. Cerrar sesión");

            System.out.print("Seleccione una opción: ");
            opcion = leerEntero();

            switch (opcion) {
                case 1 -> menuUsuarios();
                case 2 -> menuRecursos();
                case 3 -> menuPrestamos();
                case 4 -> menuConsultas();
                case 0 -> System.out.println("🔒 Sesión cerrada.");
                default -> System.out.println("❌ Opción inválida.");
            }

        } while (opcion != 0);
    }

    // ==============================
    // 🔸 MENÚ USUARIOS
    // ==============================
    private static void menuUsuarios() {
        int op;
        do {
            System.out.println("\n👥 --- Gestión de Usuarios ---");
            System.out.println("1. Listar usuarios");
            System.out.println("2. Buscar usuario por ID");
            System.out.println("3. Eliminar usuario");
            System.out.println("4. Registrar nuevo usuario");
            System.out.println("0. Volver");

            System.out.print("Seleccione una opción: ");
            op = leerEntero();

            switch (op) {
                case 1 -> biblioteca.mostrarUsuarios();
                case 2 -> {
                    System.out.print("Ingrese ID: ");
                    String id = sc.nextLine();
                    Usuario u = biblioteca.buscarUsuarioPorId(id);
                    System.out.println(u != null ? u : "❌ Usuario no encontrado.");
                }
                case 3 -> {
                    System.out.print("ID a eliminar: ");
                    String id = sc.nextLine();
                    boolean eliminado = biblioteca.eliminarUsuario(id);
                    System.out.println(eliminado ? "✅ Eliminado." : "❌ No encontrado.");
                }
                case 4 -> registrarNuevoUsuario();
                case 0 -> System.out.println("↩️ Volviendo...");
                default -> System.out.println("❌ Opción inválida.");
            }
        } while (op != 0);
    }
    private static void registrarNuevoUsuario() {
    System.out.println("\n👤 Registro de nuevo usuario");
    System.out.print("Tipo (ALUMNO / PROFESOR / INVITADO / BIBLIOTECARIO): ");
    String tipo = sc.nextLine().toUpperCase();

    System.out.print("ID: ");
    String id = sc.nextLine();
    System.out.print("Nombre: ");
    String nombre = sc.nextLine();
    System.out.print("Email: ");
    String email = sc.nextLine();
    System.out.print("Teléfono: ");
    String tel = sc.nextLine();
    System.out.print("Password: ");
    String pass = sc.nextLine();

    Usuario nuevo = null;

    switch (tipo) {
        case "ALUMNO" -> {
            System.out.print("Carrera: ");
            String carrera = sc.nextLine();
            System.out.print("Código Alumno: ");
            String codigo = sc.nextLine();
            System.out.print("Ciclo: ");
            int ciclo = leerEntero();
            nuevo = new Alumno(id, nombre, email, tel, pass, carrera, codigo, ciclo);
        }
        case "PROFESOR" -> {
            System.out.print("Facultad: ");
            String fac = sc.nextLine();
            System.out.print("Especialidad: ");
            String esp = sc.nextLine();
            System.out.print("Código Profesor: ");
            String codProf = sc.nextLine();
            nuevo = new Profesor(id, nombre, email, tel, pass, fac, esp, codProf);
        }
        case "INVITADO" -> {
            System.out.print("Tipo invitado: ");
            String tipoInv = sc.nextLine();
            System.out.print("Fecha expiración (AAAA-MM-DD): ");
            LocalDate fechaExp = LocalDate.parse(sc.nextLine());
            nuevo = new Invitado(id, nombre, email, tel, pass, tipoInv, fechaExp);
        }
        case "BIBLIOTECARIO" -> {
            System.out.print("ID Empleado: ");
            int idEmp = leerEntero();
            System.out.print("Turno: ");
            String turno = sc.nextLine();
            System.out.print("Especialidad: ");
            String esp = sc.nextLine();
            nuevo = new Bibliotecario(id, nombre, email, tel, pass, idEmp, turno, esp);
        }
        default -> System.out.println("❌ Tipo no válido.");
    }

    if (nuevo != null) {
        usuarioDAO.agregarUsuario(nuevo);          // ✅ Guardar en la base de datos
        biblioteca.registrarUsuario(nuevo);        // ✅ Guardar en memoria
        System.out.println("✅ Usuario registrado correctamente.");
    }
}


    // ==============================
    // 🔸 MENÚ RECURSOS
    // ==============================
    private static void menuRecursos() {
    // 🔄 Cargar siempre los recursos más recientes al ingresar al menú
    List<Recurso> nuevos = recursoDAO.listarRecursos();
    biblioteca.limpiarRecursos();
    for (Recurso r : nuevos) {
        biblioteca.registrarRecurso(r);
    }

    int op;
    do {
        System.out.println("\n📦 --- Gestión de Recursos ---");
        System.out.println("1. Listar recursos");
        System.out.println("2. Buscar recurso por ID");
        System.out.println("3. Eliminar recurso");
        System.out.println("4. Registrar nuevo recurso"); //agregado 
        System.out.println("0. Volver");

        System.out.print("Seleccione una opción: ");
        op = leerEntero();

        switch (op) {
            case 1 -> biblioteca.mostrarRecursos();
            case 2 -> {
                System.out.print("ID recurso: ");
                int id = leerEntero();
                Recurso r = biblioteca.buscarRecursoPorId(id);
                System.out.println(r != null ? r.consultarDetalles() : "❌ No encontrado.");
            }
            case 3 -> {
                System.out.print("ID recurso a eliminar: ");
                int id = leerEntero();
                boolean eliminado = biblioteca.eliminarRecurso(id);
                System.out.println(eliminado ? "✅ Recurso eliminado." : "❌ No se encontró el recurso.");
            }
            case 4 -> registrarNuevoRecurso();  // ⬅️ Nueva acción

            case 0 -> System.out.println("↩️ Volviendo...");
            default -> System.out.println("❌ Opción inválida.");
        }
    } while (op != 0);
}
    private static void registrarNuevoRecurso() {
    System.out.println("\n➕ Registro de Nuevo Recurso");

    System.out.println("Seleccione el tipo de recurso:");
    System.out.println("1. Libro Físico");
    System.out.println("2. Revista");
    System.out.println("3. Tablet");
    System.out.println("4. Laptop");
    System.out.println("5. Multímetro");
    System.out.println("6. Microscopio");
    System.out.println("7. Proyector");
    System.out.println("8. Libro Digital");
    System.out.println("9. Artículo Digital");
    System.out.println("10. Tesis Digital");
    System.out.println("11. Sala de Estudio");
    System.out.println("12. Sala de Lectura");

    int tipo = leerEntero();
    System.out.print("ID: ");
    int id = leerEntero();
    System.out.print("Título: ");
    String titulo = sc.nextLine();
    LocalDate fechaIngreso = LocalDate.now();  // Puedes pedir fecha manual si prefieres

    Recurso nuevo = null;

    switch (tipo) {
        case 1 -> {
            System.out.print("Ubicación estante: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Autor: ");
            String autor = sc.nextLine();
            System.out.print("Número de páginas: ");
            int numPaginas = leerEntero();

            nuevo = new LibroFisico(id, titulo, fechaIngreso, ubicacion, condicion, autor, numPaginas);
        }
        case 2 -> {
            System.out.print("Ubicación estante: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Volumen: ");
            int volumen = leerEntero();
            System.out.print("Número: ");
            int numero = leerEntero();

            nuevo = new Revista(id, titulo, fechaIngreso, ubicacion, condicion, volumen, numero);
        }
        case 3 -> {
            System.out.print("Ubicación estante: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Marca: ");
            String marca = sc.nextLine();
            System.out.print("Modelo: ");
            String modelo = sc.nextLine();
            System.out.print("Tamaño pantalla (pulgadas): ");
            double tamano = Double.parseDouble(sc.nextLine());

            nuevo = new Tablet(id, titulo, fechaIngreso, ubicacion, condicion, marca, modelo, tamano);
        }
        case 4 -> {
            System.out.print("Ubicación estante: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Marca: ");
            String marca = sc.nextLine();
            System.out.print("Modelo: ");
            String modelo = sc.nextLine();
            System.out.print("RAM (GB): ");
            int ram = leerEntero();
            System.out.print("Procesador: ");
            String proc = sc.nextLine();

            nuevo = new Laptop(id, titulo, fechaIngreso, ubicacion, condicion, marca, modelo, ram, proc);
        }
        case 5 -> {
            System.out.print("Ubicación estante: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Descripción: ");
            String desc = sc.nextLine();
            System.out.print("Rango: ");
            String rango = sc.nextLine();

            nuevo = new Multimetro(id, titulo, fechaIngreso, ubicacion, condicion, desc, rango);
        }
        case 6 -> {
            System.out.print("Ubicación estante: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Descripción: ");
            String desc = sc.nextLine();
            System.out.print("Aumentos máximos: ");
            int aumentos = leerEntero();

            nuevo = new Microscopio(id, titulo, fechaIngreso, ubicacion, condicion, desc, aumentos);
        }
        case 7 -> {
            System.out.print("Ubicación estante: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Descripción: ");
            String desc = sc.nextLine();
            System.out.print("Resolución: ");
            String resolucion = sc.nextLine();
            System.out.print("Brillo: ");
            int brillo = leerEntero();

            nuevo = new Proyector(id, titulo, fechaIngreso, ubicacion, condicion, desc, resolucion, brillo);
        }
        case 8 -> {
            System.out.print("Formato: ");
            String formato = sc.nextLine();
            System.out.print("URL de acceso: ");
            String url = sc.nextLine();
            System.out.print("Autor: ");
            String autor = sc.nextLine();
            System.out.print("Número de páginas: ");
            int numPaginas = leerEntero();

            nuevo = new LibroDigital(id, titulo, fechaIngreso, formato, url, autor, numPaginas);
        }
        case 9 -> {
            System.out.print("Formato: ");
            String formato = sc.nextLine();
            System.out.print("URL de acceso: ");
            String url = sc.nextLine();
            System.out.print("Revista de origen: ");
            String revista = sc.nextLine();
            System.out.print("DOI: ");
            String doi = sc.nextLine();

            nuevo = new ArticuloDigital(id, titulo, fechaIngreso, formato, url, revista, doi);
        }
        case 10 -> {
            System.out.print("Formato: ");
            String formato = sc.nextLine();
            System.out.print("URL de acceso: ");
            String url = sc.nextLine();
            System.out.print("Autor: ");
            String autor = sc.nextLine();
            System.out.print("Universidad: ");
            String universidad = sc.nextLine();

            nuevo = new TesisDigital(id, titulo, fechaIngreso, formato, url, autor, universidad);
        }
        case 11 -> {
            System.out.print("Ubicación: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Capacidad: ");
            int capacidad = leerEntero();
            System.out.print("Número de mesas: ");
            int mesas = leerEntero();

            nuevo = new SalaEstudio(id, titulo, fechaIngreso, ubicacion, condicion, capacidad, mesas);
        }
        case 12 -> {
            System.out.print("Ubicación: ");
            String ubicacion = sc.nextLine();
            System.out.print("Condición: ");
            String condicion = sc.nextLine();
            System.out.print("Capacidad: ");
            int capacidad = leerEntero();
            System.out.print("Número de sillas: ");
            int sillas = leerEntero();

            nuevo = new SalaLectura(id, titulo, fechaIngreso, ubicacion, condicion, capacidad, sillas);
        }
        default -> System.out.println("❌ Tipo inválido.");
    }

    if (nuevo != null) {
        biblioteca.registrarRecurso(nuevo);  // en memoria
        recursoDAO.insertarRecurso(nuevo);   // en BD
        System.out.println("✅ Recurso agregado correctamente.");
    }
}


    // ==============================
    // 🔸 MENÚ PRÉSTAMOS
    // ==============================
    private static void menuPrestamos() {
        int op;
        do {
            System.out.println("\n🔁 --- Gestión de Préstamos ---");
            System.out.println("1. Registrar préstamo");
            System.out.println("2. Registrar devolución");
            System.out.println("3. Listar préstamos desde base de datos");
            System.out.println("4. Mostrar penalizaciones");

            System.out.println("0. Volver");

            System.out.print("Seleccione una opción: ");
            op = leerEntero();

            switch (op) {
                case 1 -> {
                    System.out.print("ID usuario: ");
                    String idU = sc.nextLine();

                    // 🚫 Validar penalización ANTES de permitir préstamo
                    // Verificar si tiene penalización
                    if (devolucionDAO.tienePenalizacion(idU)) {
                        System.out.println("⛔ El usuario tiene penalizaciones y no puede realizar préstamos.");
                        return;
                    }

                    // Verificar si ya tiene 2 préstamos activos
                    int prestamosActivos = prestamoDAO.contarPrestamosActivosPorUsuario(idU);
                    if (prestamosActivos >= 2) {
                        System.out.println("⛔ El usuario ya tiene 2 préstamos activos. No puede solicitar más.");
                        return;
}



                    Usuario u = biblioteca.buscarUsuarioPorId(idU);

                    System.out.print("ID recurso: ");
                    int idR = leerEntero();
                    Recurso r = biblioteca.buscarRecursoPorId(idR);

                    if (u != null && r != null) {
                    biblioteca.registrarPrestamo(u, r);
                    } else {
                    System.out.println("❌ Usuario o recurso no válido.");
    }
}

                case 2 -> {
                    System.out.print("ID préstamo: ");
                    int id = leerEntero();
                    Prestamo p = biblioteca.buscarPrestamoPorId(id);
                    if (p != null) {
                        biblioteca.registrarDevolucion(p);
                    } else {
                        System.out.println("❌ Préstamo no encontrado.");
                    }
                }
                case 3 -> biblioteca.mostrarPrestamosBD();
                case 4 -> biblioteca.mostrarDevolucionesBD();
                case 0 -> System.out.println("↩️ Volviendo...");
                default -> System.out.println("❌ Opción inválida.");
            }
        } while (op != 0);
    }

    // ==============================
    // 🔸 CONSULTAS
    // ==============================
    private static void menuConsultas() {
        System.out.println("\n📊 --- Consultas ---");
        biblioteca.mostrarPrestamosBD();
    }

    // ==============================
    // 🔸 UTILIDAD
    // ==============================
    private static int leerEntero() {
        try {
            return Integer.parseInt(sc.nextLine());
        } catch (NumberFormatException e) {
            return -1;
        }
    }
    
}

package sgbu.model;

import java.time.LocalDate;

public class TesisDigital extends RecursoDigital {
    private String autor;
    private String universidad;

    public TesisDigital(int id, String titulo, LocalDate fechaIngreso,
                        String formato, String urlAcceso, String autor, String universidad) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.autor = autor;
        this.universidad = universidad;
    }

    public String consultarResumen() {
        return "Resumen simulado de la tesis.";
    }

    @Override
    public String consultarDetalles() {
        return String.format("TesisDigital[id=%d, titulo=%s, autor=%s, universidad=%s]",
                id, titulo, autor, universidad);
    }
    public String getAutor() {
        return autor;
    }

    @Override
    public String getUrlAcceso() {
        return urlAcceso;
    }

    @Override
    public String getFormato() {
        return formato;
    }

    public String getUniversidad() {
        return universidad;
    }
    
}



RecursoDigital
import java.time.LocalDate;

public abstract class RecursoDigital extends Recurso {
    protected String formato;
    protected String urlAcceso;

    public RecursoDigital(int id, String titulo, LocalDate fechaIngreso, String formato, String urlAcceso) {
        super(id, titulo, fechaIngreso);
        this.formato = formato;
        this.urlAcceso = urlAcceso;
    }

    public String getFormato() { return formato; }
    public String getUrlAcceso() { return urlAcceso; }

    public void visualizarOnline() {
        System.out.println("Abriendo visor para: " + titulo + " en " + urlAcceso);
    }

    public void descargar() {
        System.out.println("Descargando: " + titulo + " formato " + formato);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Digital[id=%d,titulo=%s,formato=%s,url=%s]", id, titulo, formato, urlAcceso);
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
