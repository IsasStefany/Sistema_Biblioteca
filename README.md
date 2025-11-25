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
