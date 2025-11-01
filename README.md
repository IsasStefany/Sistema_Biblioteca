
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
ArticuloDigital
import java.time.LocalDate;

public class ArticuloDigital extends RecursoDigital {
    private String revistaOrigen;
    private String doi;

    public ArticuloDigital(int id, String titulo, LocalDate fechaIngreso, String formato, String urlAcceso, String revistaOrigen, String doi) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.revistaOrigen = revistaOrigen;
        this.doi = doi;
    }

    public String generarReferencia() {
        return revistaOrigen + " - DOI:" + doi;
    }

    @Override
    public String consultarDetalles() {
        return String.format("ArticuloDigital[id=%d,titulo=%s,revista=%s,doi=%s]", id, titulo, revistaOrigen, doi);
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
Dispositivo
import java.time.LocalDate;

public abstract class Dispositivo extends RecursoFisico {
    protected String marca;
    protected String modelo;

    public Dispositivo(int id, String titulo, java.time.LocalDate fechaIngreso, String ubicacion, String condicion, String marca, String modelo) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.marca = marca;
        this.modelo = modelo;
    }

    public void encender() { System.out.println(titulo + " encendido."); }
    public void apagar() { System.out.println(titulo + " apagado."); }

    @Override
    public String consultarDetalles() {
        return String.format("Dispositivo[id=%d,titulo=%s,marca=%s,modelo=%s,disp=%b]", id, titulo, marca, modelo, disponible);
    }
}
Instrumento
import java.time.LocalDate;

public abstract class Instrumento extends RecursoFisico {
    protected String descripcion;
    public Instrumento(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, String descripcion) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.descripcion = descripcion;
    }

    @Override
    public String consultarDetalles() {
        return String.format("Instrumento[id=%d,titulo=%s,desc=%s]", id, titulo, descripcion);
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
Laptop
import java.time.LocalDate;

public class Laptop extends Dispositivo {
    private int ramGb;
    private String procesador;

    public Laptop(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, String marca, String modelo, int ramGb, String procesador) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, marca, modelo);
        this.ramGb = ramGb;
        this.procesador = procesador;
    }

    public void instalarSoftware(String nombre) {
        System.out.println("Instalando " + nombre + " en " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Laptop[id=%d,titulo=%s,marca=%s,ram=%dGB]", id, titulo, marca, ramGb);
    }
}
LibroDigital
import java.time.LocalDate;

public class LibroDigital extends RecursoDigital {
    private String autor;
    private int numPaginas;

    public LibroDigital(int id, String titulo, LocalDate fechaIngreso, String formato, String urlAcceso, String autor, int numPaginas) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.autor = autor;
        this.numPaginas = numPaginas;
    }

    public String generarCitaAPA() {
        return autor + ". (" + fechaIngreso.getYear() + "). " + titulo + ".";
    }

    @Override
    public String consultarDetalles() {
        return String.format("LibroDigital[id=%d,titulo=%s,autor=%s,formato=%s]", id, titulo, autor, formato);
    }
}
LibroFisico
import java.time.LocalDate;

public class LibroFisico extends RecursoFisico {
    private String autor;
    private int numPaginas;

    public LibroFisico(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, String autor, int numPaginas) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.autor = autor;
        this.numPaginas = numPaginas;
    }

    public String getAutor() { return autor; }
    public int getNumPaginas() { return numPaginas; }

    public String consultarIndice() {
        return "Índice (simulado) del libro: " + titulo;
    }

    @Override
    public String consultarDetalles() {
        return String.format("Libro[id=%d,titulo=%s,autor=%s,pag=%d,disp=%b]", id, titulo, autor, numPaginas, disponible);
    }
}

Microscopio
public class Microscopio extends Instrumento {
    private int aumentosMax;

    public Microscopio(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, String descripcion, int aumentosMax) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, descripcion);
        this.aumentosMax = aumentosMax;
    }

    public void calibrar() {
        System.out.println("Calibrando " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Microscopio[id=%d,titulo=%s,aumentos=%d]", id, titulo, aumentosMax);
    }
}
Multimetro
import java.time.LocalDate;

public class Multimetro extends Instrumento {
    private String rango;

    public Multimetro(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, String descripcion, String rango) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, descripcion);
        this.rango = rango;
    }

    public boolean autoverificar() {
        System.out.println("Autoverificando " + titulo);
        return true;
    }

    @Override
    public String consultarDetalles() {
        return String.format("Multimetro[id=%d,titulo=%s,rango=%s]", id, titulo, rango);
    }
}
Penalización
public class Penalizacion {
    private static final double TARIFA_PORDIA = 1.5; // ejemplo
    private int diasRetraso;

    public Penalizacion(int diasRetraso) {
        this.diasRetraso = diasRetraso;
    }

    public int getDiasRetraso() { return diasRetraso; }

    public double calcularMonto() {
        return diasRetraso * TARIFA_PORDIA;
    }

    public void notificarUsuario(Usuario u) {
        System.out.println("Notificando a " + u.getNombre() + " sobre penalización de " + calcularMonto());
    }
}
Prestamo
import java.time.LocalDate;
import java.time.temporal.ChronoUnit;

public class Prestamo {
    private int idPrestamo;
    private Usuario usuario;
    private Recurso recurso;
    private LocalDate fechaInicio;
    private LocalDate fechaDevolucion;
    private String estado; // vigente, devuelto, atrasado
    private Penalizacion penalizacion; // composición

    private static int contador = 1;

    public Prestamo(Usuario usuario, Recurso recurso, LocalDate fechaInicio, LocalDate fechaDevolucion) {
        this.idPrestamo = contador++;
        this.usuario = usuario;
        this.recurso = recurso;
        this.fechaInicio = fechaInicio;
        this.fechaDevolucion = fechaDevolucion;
        this.estado = "vigente";
    }

    public int getIdPrestamo() { return idPrestamo; }
    public Usuario getUsuario() { return usuario; }
    public Recurso getRecurso() { return recurso; }
    public LocalDate getFechaInicio() { return fechaInicio; }
    public LocalDate getFechaDevolucion() { return fechaDevolucion; }
    public String getEstado() { return estado; }

    public void devolverRecurso() {
        LocalDate hoy = LocalDate.now();
        if (hoy.isAfter(fechaDevolucion)) {
            estado = "atrasado";
            this.penalizacion = generarPenalizacion();
        } else {
            estado = "devuelto";
        }
        recurso.setDisponible(true);
        System.out.println("Préstamo #" + idPrestamo + " devuelto. Estado: " + estado);
    }

    public boolean esAtrasado() {
        return LocalDate.now().isAfter(fechaDevolucion);
    }

    public Penalizacion generarPenalizacion() {
        long dias = ChronoUnit.DAYS.between(fechaDevolucion, LocalDate.now());
        if (dias <= 0) return null;
        this.penalizacion = new Penalizacion((int) dias);
        return this.penalizacion;
    }

    @Override
    public String toString() {
        return String.format("Prestamo[id=%d,usuario=%s,recurso=%s,ini=%s,fin=%s,estado=%s]",
                idPrestamo, usuario.getNombre(), recurso.getTitulo(), fechaInicio, fechaDevolucion, estado);
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
Proyector
import java.time.LocalDate;

public class Proyector extends Instrumento {
    private String resolucion;
    private int brillo;

    public Proyector(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, String descripcion, String resolucion, int brillo) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, descripcion);
        this.resolucion = resolucion;
        this.brillo = brillo;
    }

    public void proyectarArchivo(String nombreArchivo) {
        System.out.println("Proyectando " + nombreArchivo + " en " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Proyector[id=%d,titulo=%s,res=%s,brillo=%d]", id, titulo, resolucion, brillo);
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

Reservable
public interface Reservable {
    boolean reservar(String quien, java.time.LocalDate fechaInicio, java.time.LocalDate fechaFin);
    void liberar();
    boolean estaReservado();
}
Revista
import java.time.LocalDate;
import java.util.List;
import java.util.Arrays;

public class Revista extends RecursoFisico {
    private int volumen;
    private int numero;

    public Revista(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, int volumen, int numero) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.volumen = volumen;
        this.numero = numero;
    }

    public List<String> consultarArticulos() {
        return Arrays.asList("Artículo 1", "Artículo 2");
    }

    @Override
    public String consultarDetalles() {
        return String.format("Revista[id=%d,titulo=%s,vol=%d,num=%d,disp=%b]", id, titulo, volumen, numero, disponible);
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
Tablet
import java.time.LocalDate;

public class Tablet extends Dispositivo {
    private double tamanoPantalla;

    public Tablet(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, String marca, String modelo, double tamanoPantalla) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, marca, modelo);
        this.tamanoPantalla = tamanoPantalla;
    }

    public void abrirApp(String nombre) {
        System.out.println("Abriendo " + nombre + " en " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Tablet[id=%d,titulo=%s,marca=%s,pantalla=%.1fin]", id, titulo, marca, tamanoPantalla);
    }
}
TesisDigital
import java.time.LocalDate;

public class TesisDigital extends RecursoDigital {
    private String autor;
    private String universidad;

    public TesisDigital(int id, String titulo, LocalDate fechaIngreso, String formato, String urlAcceso, String autor, String universidad) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.autor = autor;
        this.universidad = universidad;
    }

    public String consultarResumen() { return "Resumen de tesis (simulado)"; }

    @Override
    public String consultarDetalles() {
        return String.format("Tesis[id=%d,titulo=%s,autor=%s,uni=%s]", id, titulo, autor, universidad);
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
