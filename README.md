CODIGO DE SISTEMA DE GESTION DE BIBLIOTECA UNIVERSITARIA
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
    public String getCarrera() {
    return carrera;
}

    public String getCodigoAlumno() {
        return codigoAlumno;
    }

    public int getCiclo() {
        return ciclo;
    }
    public String getTelefono() {
        return telefono;
    }
    @Override
    public String consultarDetalles() {
    return String.format("Alumno[id=%s, nombre=%s, carrera=%s, ciclo=%d]",
            idUsuario, nombre, carrera, ciclo);
}

}
//AlumnoDAO
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import sgbu.Alumno;
import sgbu.DBConnection;

public class AlumnoDAO {

    // Agregar un alumno
    public void agregarAlumno(Alumno alumno) {
        String sql = "INSERT INTO Alumnos (idUsuario, nombre, email, telefono, password, carrera, codigoAlumno, ciclo) "
                   + "VALUES (?, ?, ?, ?, ?, ?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, alumno.getIdUsuario());
            stmt.setString(2, alumno.getNombre());
            stmt.setString(3, alumno.getEmail());
            stmt.setString(4, alumno.getTelefono());     // CORREGIDO
            stmt.setString(5, alumno.getPassword());     // CORREGIDO
            stmt.setString(6, alumno.getCarrera());
            stmt.setString(7, alumno.getCodigoAlumno());
            stmt.setInt(8, alumno.getCiclo());

            stmt.executeUpdate();
            System.out.println("✅ Alumno agregado correctamente a la base de datos.");

        } catch (SQLException e) {
            System.out.println("❌ Error al agregar alumno: " + e.getMessage());
        }
    }

    // Listar alumnos
    public List<Alumno> listarAlumnos() {
        List<Alumno> lista = new ArrayList<>();
        String sql = "SELECT * FROM Alumnos";

        try (Connection conn = DBConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                Alumno a = new Alumno(
                        rs.getString("idUsuario"),
                        rs.getString("nombre"),
                        rs.getString("email"),
                        rs.getString("telefono"),
                        rs.getString("password"),
                        rs.getString("carrera"),
                        rs.getString("codigoAlumno"),
                        rs.getInt("ciclo")
                );
                lista.add(a);
            }

        } catch (SQLException e) {
            System.out.println("❌ Error al listar alumnos: " + e.getMessage());
        }
        return lista;
    }

    // Buscar alumno por ID
    public Alumno buscarAlumnoPorId(String idUsuario) {
        String sql = "SELECT * FROM Alumnos WHERE idUsuario = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, idUsuario);
            ResultSet rs = stmt.executeQuery();

            if (rs.next()) {
                return new Alumno(
                        rs.getString("idUsuario"),
                        rs.getString("nombre"),
                        rs.getString("email"),
                        rs.getString("telefono"),
                        rs.getString("password"),
                        rs.getString("carrera"),
                        rs.getString("codigoAlumno"),
                        rs.getInt("ciclo")
                );
            }

        } catch (SQLException e) {
            System.out.println("❌ Error al buscar alumno: " + e.getMessage());
        }
        return null;
    }

    // Actualizar alumno
    public void actualizarAlumno(Alumno alumno) {
        String sql = "UPDATE Alumnos "
                   + "SET nombre=?, email=?, telefono=?, password=?, carrera=?, codigoAlumno=?, ciclo=? "
                   + "WHERE idUsuario=?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, alumno.getNombre());
            stmt.setString(2, alumno.getEmail());
            stmt.setString(3, alumno.getTelefono());  // CORREGIDO
            stmt.setString(4, alumno.getPassword());  // CORREGIDO
            stmt.setString(5, alumno.getCarrera());
            stmt.setString(6, alumno.getCodigoAlumno());
            stmt.setInt(7, alumno.getCiclo());
            stmt.setString(8, alumno.getIdUsuario());

            int filas = stmt.executeUpdate();

            if (filas > 0) {
                System.out.println("✅ Alumno actualizado correctamente.");
            } else {
                System.out.println("⚠️ No se encontró un alumno con ese ID.");
            }

        } catch (SQLException e) {
            System.out.println("❌ Error al actualizar alumno: " + e.getMessage());
        }
    }

    // Eliminar alumno
    public void eliminarAlumno(String idUsuario) {
        String sql = "DELETE FROM Alumnos WHERE idUsuario = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, idUsuario);

            int filas = stmt.executeUpdate();

            if (filas > 0) {
                System.out.println("🗑️ Alumno eliminado correctamente.");
            } else {
                System.out.println("⚠️ No se encontró un alumno con ese ID.");
            }

        } catch (SQLException e) {
            System.out.println("❌ Error al eliminar alumno: " + e.getMessage());
        }
    }
}
//Ambiente
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
//ArchivoUtil
import java.io.*;
import java.util.*;
import java.time.LocalDate;

import java.io.*;
import java.util.*;
import sgbu.*; // ajusta si tus clases están en otro paquete

public class ArchivoUtil {

    /**
     * Lee el archivo de alumnos (formato por línea: idUsuario;nombre;email;telefono;password;carrera;codigoAlumno;ciclo)
     * Para cada alumno:
     *  - Si no existe en la BD -> lo inserta usando AlumnoDAO
     *  - Si no existe en la memoria de la Biblioteca -> lo registra en la biblioteca
     */
    public static void cargarAlumnos(Biblioteca biblioteca, String rutaArchivo) {
        AlumnoDAO dao = new AlumnoDAO();
        File archivo = new File(rutaArchivo);

        if (!archivo.exists()) {
            System.out.println("⚠️ Archivo no encontrado: " + rutaArchivo);
            return;
        }

        int cargados = 0;
        try (BufferedReader br = new BufferedReader(new FileReader(archivo))) {
            String linea;
            while ((linea = br.readLine()) != null) {
                if (linea.trim().isEmpty()) continue;

                String[] campos = linea.split(";");
                if (campos.length < 8) {
                    System.out.println("⚠️ Línea inválida (esperado 8 campos): " + linea);
                    continue;
                }

                String id = campos[0].trim();
                String nombre = campos[1].trim();
                String email = campos[2].trim();
                String telefono = campos[3].trim();
                String password = campos[4].trim();
                String carrera = campos[5].trim();
                String codigoAlumno = campos[6].trim();
                int ciclo;
                try {
                    ciclo = Integer.parseInt(campos[7].trim());
                } catch (NumberFormatException ex) {
                    System.out.println("⚠️ Ciclo inválido en línea: " + linea);
                    continue;
                }

                Alumno a = new Alumno(id, nombre, email, telefono, password, carrera, codigoAlumno, ciclo);

                // Primero: verificar BD
                Alumno existenteBD = dao.buscarAlumnoPorId(a.getIdUsuario());
                if (existenteBD == null) {
                    dao.agregarAlumno(a);
                    System.out.println("✅ Alumno agregado a la BD: " + a.getIdUsuario() + " - " + a.getNombre());
                } else {
                    // opcional: podrías actualizar datos si difieren
                    // dao.actualizarAlumno(a); // solo si quieres forzar sincronización
                    System.out.println("🔁 Alumno ya existe en BD: " + a.getIdUsuario());
                }

                // Segundo: registrar en memoria (Biblioteca) solo si no existe
                if (biblioteca.buscarUsuarioPorId(a.getIdUsuario()) == null) {
                    biblioteca.registrarUsuario(a);
                    cargados++;
                } else {
                    System.out.println("⚠️ Alumno ya en memoria, no duplicado: " + a.getIdUsuario());
                }
            }

            System.out.println("📚 Proceso archivo completado. " + cargados + " alumnos añadidos a la memoria.");
        } catch (IOException e) {
            System.err.println("❌ Error al leer archivo: " + e.getMessage());
        }
    }


    /**
     * Guarda la lista de alumnos de la biblioteca en un archivo (sobrescribe).
     * Formato por línea: idUsuario;nombre;email;telefono;password;carrera;codigoAlumno;ciclo
     */
    public static void guardarAlumnos(Biblioteca biblioteca, String rutaArchivo) {
    try (PrintWriter pw = new PrintWriter(new FileWriter(rutaArchivo))) {
        for (Usuario u : biblioteca.consultarUsuarios()) {
            if (u instanceof Alumno a) {
                pw.println(String.join(";",
                        a.getIdUsuario(),
                        a.getNombre(),
                        a.getEmail(),
                        a.getTelefono(),
                        a.password, // atributo protegido de Usuario
                        a.getCarrera(),
                        a.getCodigoAlumno(),
                        String.valueOf(a.getCiclo())
                ));
            }
        }
        System.out.println("💾 Alumnos guardados en " + rutaArchivo);
    } catch (IOException e) {
        System.err.println("❌ Error al guardar archivo: " + e.getMessage());
    }
}

}

//ArticuloDigital
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
//Biblioteca
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

public class Biblioteca {
    private int idBiblioteca;
    private String nombre;
    private String direccion;
    private PrestamoDAO prestamoDAO = new PrestamoDAO();
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
    public void registrarUsuario(Usuario u) {
    if (buscarUsuarioPorId(u.getIdUsuario()) == null) {
        usuarios.add(u);
        // System.out.println("Usuario agregado: " + u.getIdUsuario());
    } else {
        // ya existe en memoria
        // System.out.println("Usuario duplicado ignorado en memoria: " + u.getIdUsuario());
    }
}


    public boolean eliminarUsuario(String idUsuario) { return usuarios.removeIf(u -> u.getIdUsuario().equals(idUsuario));
 }
    public List<Usuario> consultarUsuarios() { return new ArrayList<>(usuarios); }
    public Usuario buscarUsuarioPorId(String idUsuario) {
    for (Usuario u : usuarios) {
        if (u.getIdUsuario().equals(idUsuario)) return u;
    }
    return null;
}


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
        System.out.println("⚠️ Recurso no disponible: " + recurso.getTitulo());
        return null;
    }

    LocalDate inicio = LocalDate.now();
    LocalDate fin = inicio.plusDays(7); // Préstamo por 7 días
    Prestamo p = new Prestamo(usuario, recurso, inicio, fin);
    p.setEstado("Activo"); // ✅ Estado inicial

    recurso.setDisponible(false);
    prestamos.add(p);

    // Guardar también en la base de datos
    prestamoDAO.registrarPrestamo(p);

    System.out.println("✅ Préstamo registrado correctamente: " + p);
    return p;
}
    // ======================================================
// Registrar préstamos cargados desde la Base de Datos
// (NO crea un préstamo nuevo, solo lo añade a memoria)
// ======================================================
    public void registrarPrestamoDesdeBD(Prestamo p) {
    if (p == null) return;

    // Añadir a lista interna
    prestamos.add(p);

    // Si el préstamo sigue activo, marcar recurso como NO disponible
    if (p.getEstado() != null && p.getEstado().equalsIgnoreCase("Activo")) {
        p.getRecurso().setDisponible(false);
    }
}



    public List<Prestamo> consultarPrestamos() { return new ArrayList<>(prestamos); }

    public List<Prestamo> consultarPrestamosPorUsuario(String idUsuario) {
        return prestamos.stream()
        .filter(p -> p.getUsuario().getIdUsuario().equals(idUsuario))
        .collect(Collectors.toList());
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

// Dentro de la clase Biblioteca
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
public void mostrarPrestamosBD() {
    List<Prestamo> lista = prestamoDAO.listarPrestamos();

    System.out.println("\n--- Préstamos en la base de datos ---");
    if (lista.isEmpty()) {
        System.out.println("📭 No hay préstamos registrados en SQL Server.");
    } else {
        for (Prestamo p : lista) {
            System.out.println("ID Préstamo: " + p.getIdPrestamo() +
                    " | Estado: " + p.getEstado() +
                    " | Fecha Inicio: " + p.getFechaInicio() +
                    " | Fecha Devolución: " + p.getFechaDevolucion());
        }
    }
}
    // ======================================================
// Buscar préstamo por ID en la memoria local
// ======================================================
public Prestamo buscarPrestamoPorId(int idPrestamo) {
    for (Prestamo p : prestamos) {
        if (p.getIdPrestamo() == idPrestamo) {
            return p;
        }
    }
    return null;
}

// ======================================================
// Registrar devolución de un préstamo
// ======================================================
public Devolucion registrarDevolucion(Prestamo p) {

    if (p == null) {
        System.out.println("❌ No se puede registrar devolución: préstamo es null.");
        return null;
    }

    // Si ya está devuelto no permitir doble devolución
    if (p.getEstado() != null && p.getEstado().equalsIgnoreCase("Devuelto")) {
        System.out.println("⚠️ Este préstamo ya fue devuelto.");
        return null;
    }

    // Crear la devolución
    LocalDate hoy = LocalDate.now();
    String estado = hoy.isAfter(p.getFechaDevolucion()) ? "Retraso" : "Devuelto";

    Devolucion d = new Devolucion(
            p.getIdPrestamo(),
            hoy,
            estado
    );

    // Guardar en BD
    DevolucionDAO devolucionDAO = new DevolucionDAO();
    devolucionDAO.insertar(d);

    // Actualizar estado del préstamo
    p.setEstado("Devuelto");

    // Hacer el recurso disponible otra vez
    p.getRecurso().setDisponible(true);

    System.out.println("✅ Devolución registrada correctamente.");
    return d;
}

}
//Bibliotecario
import java.time.LocalDate;
import java.util.List;

public class Bibliotecario extends Usuario {
    
    private String turno;
    private String especialidad;
    private Biblioteca biblioteca; // referencia bidireccional

    public Bibliotecario(String idUsuario, String nombre, String email, String telefono, String password, String turno, String especialidad) {
        super(idUsuario, nombre, email, telefono, password);
        
        this.turno = turno;
        this.especialidad = especialidad;
    }
    public String getTurno() {
        return turno;
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
        return "Bibliotecario " + nombre + " (Empleado " + idUsuario + ")";
    }
    @Override
    public String consultarDetalles() {
    return String.format("Bibliotecario[id=%s, nombre=%s, turno=%s, especialidad=%s]",
            idUsuario, nombre, turno, especialidad);
}

}

//DBConnection
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
            System.out.println("âŒ Error al conectar: " + e.getMessage());
            return null;
        }
    }
}
//Devolución
import java.time.LocalDate;

public class Devolucion {

    private int idDevolucion;
    private int idPrestamo;
    private LocalDate fechaDevolucion;
    private String estado; // "devuelto", "retraso", etc.

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
}

//DevolucionDAO
import java.sql.*;
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

import sgbu.DBConnection;
import sgbu.Devolucion;

public class DevolucionDAO {

    // ============================================================
    // INSERTAR DEVOLUCIÓN
    // ============================================================
    public boolean insertar(Devolucion d) {
        String sql = "INSERT INTO Devoluciones (idPrestamo, fechaDevolucion, estado) VALUES (?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, d.getIdPrestamo());
            stmt.setDate(2, Date.valueOf(d.getFechaDevolucion()));
            stmt.setString(3, d.getEstado());

            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.out.println("Error al insertar devolución: " + e.getMessage());
            return false;
        }
    }

    // ============================================================
    // LISTAR TODAS LAS DEVOLUCIONES
    // ============================================================
    public List<Devolucion> listarTodos() {
        List<Devolucion> lista = new ArrayList<>();
        String sql = "SELECT * FROM Devoluciones";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql);
             ResultSet rs = stmt.executeQuery()) {

            while (rs.next()) {
                lista.add(mapDevolucion(rs));
            }

        } catch (SQLException e) {
            System.out.println("Error al listar devoluciones: " + e.getMessage());
        }

        return lista;
    }

    // ============================================================
    // BUSCAR DEVOLUCIÓN POR ID
    // ============================================================
    public Devolucion buscarPorId(int idDevolucion) {
        String sql = "SELECT * FROM Devoluciones WHERE idDevolucion = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, idDevolucion);
            ResultSet rs = stmt.executeQuery();

            if (rs.next()) {
                return mapDevolucion(rs);
            }

        } catch (SQLException e) {
            System.out.println("Error al buscar devolución: " + e.getMessage());
        }

        return null;
    }

    // ============================================================
    // ACTUALIZAR DEVOLUCIÓN
    // ============================================================
    public boolean actualizar(Devolucion d) {
        String sql = "UPDATE Devoluciones SET idPrestamo = ?, fechaDevolucion = ?, estado = ? WHERE idDevolucion = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, d.getIdPrestamo());
            stmt.setDate(2, Date.valueOf(d.getFechaDevolucion()));
            stmt.setString(3, d.getEstado());
            stmt.setInt(4, d.getIdDevolucion());

            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.out.println("Error al actualizar devolución: " + e.getMessage());
            return false;
        }
    }

    // ============================================================
    // ELIMINAR DEVOLUCIÓN
    // ============================================================
    public boolean eliminar(int idDevolucion) {
        String sql = "DELETE FROM Devoluciones WHERE idDevolucion = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, idDevolucion);

            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.out.println("Error al eliminar devolución: " + e.getMessage());
            return false;
        }
    }

    // ============================================================
    // MAPEO ResultSet → OBJETO
    // ============================================================
    private Devolucion mapDevolucion(ResultSet rs) throws SQLException {
        return new Devolucion(
                rs.getInt("idDevolucion"),
                rs.getInt("idPrestamo"),
                rs.getDate("fechaDevolucion").toLocalDate(),
                rs.getString("estado")
        );
    }
}
//Dispositivo
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
//Instrumento
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
//Invitado
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
    public String getTipoInvitado() {
        return tipoInvitado;
}

    public java.time.LocalDate getFechaExpiracion() {
        return fechaExpiracion;
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
    @Override
    public String consultarDetalles() {
    return String.format("Invitado[id=%s, nombre=%s, tipo=%s, expira=%s]",
            idUsuario, nombre, tipoInvitado, fechaExpiracion);
}

}
//Laptop
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
//LibroDigital
import java.time.LocalDate;

public class LibroDigital extends RecursoDigital {
    private String autor;
    private int numPaginas;

    public LibroDigital(int id, String titulo, LocalDate fechaIngreso, String formato, String urlAcceso, String autor, int numPaginas) {
        super(id, titulo, fechaIngreso, formato, urlAcceso);
        this.autor = autor;
        this.numPaginas = numPaginas;
    }
    public String getAutor() { return autor; }
    public int getNumPaginas() { return numPaginas; }
    public String generarCitaAPA() {
        return autor + ". (" + fechaIngreso.getYear() + "). " + titulo + ".";
    }

    @Override
    public String consultarDetalles() {
        return String.format("LibroDigital[id=%d,titulo=%s,autor=%s,formato=%s]", id, titulo, autor, formato);
    }
}
//LibroDigitalDAO
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class LibroDigitalDAO {

    public void insertarLibroDigital(LibroDigital l) {
        String sql = "INSERT INTO LibrosDigitales (id, titulo, fechaIngreso, formato, urlAcceso, autor, numPaginas) VALUES (?, ?, ?, ?, ?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, l.getId());
            stmt.setString(2, l.getTitulo());
            stmt.setDate(3, java.sql.Date.valueOf(l.getFechaIngreso()));
            stmt.setString(4, l.getFormato());
            stmt.setString(5, l.getUrlAcceso());
            stmt.setString(6, l.getAutor());
            stmt.setInt(7, l.getNumPaginas());

            stmt.executeUpdate();
            System.out.println("Libro digital registrado.");

        } catch (SQLException e) {
            System.out.println("Error insertando libro digital: " + e.getMessage());
        }
    }

    public List<LibroDigital> listarLibrosDigitales() {
        List<LibroDigital> lista = new ArrayList<>();
        String sql = "SELECT * FROM LibrosDigitales";

        try (Connection conn = DBConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                LibroDigital ld = new LibroDigital(
                        rs.getInt("id"),
                        rs.getString("titulo"),
                        rs.getDate("fechaIngreso").toLocalDate(),
                        rs.getString("formato"),
                        rs.getString("urlAcceso"),
                        rs.getString("autor"),
                        rs.getInt("numPaginas")
                );
                lista.add(ld);
            }

        } catch (SQLException e) {
            System.out.println("Error listando libros digitales: " + e.getMessage());
        }

        return lista;
    }
}
//LibroFisico
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
//LibroFisicoDAO
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import sgbu.DBConnection;
import sgbu.LibroFisico;    // <- CORRECCIÓN: paquete real de tu clase

public class LibroFisicoDAO {

    public boolean agregarLibro(LibroFisico libro) {
        String sql = "INSERT INTO LibrosFisicos (id, titulo, fechaIngreso, ubicacion, condicion, autor, numPaginas, disponible) "
                   + "VALUES (?, ?, ?, ?, ?, ?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, libro.getId());
            stmt.setString(2, libro.getTitulo());
            // libro.getFechaIngreso() es LocalDate en tu modelo -> convertir a java.sql.Date
            stmt.setDate(3, Date.valueOf(libro.getFechaIngreso()));
            stmt.setString(4, libro.getUbicacionEstante());
            stmt.setString(5, libro.getCondicion());
            stmt.setString(6, libro.getAutor());
            stmt.setInt(7, libro.getNumPaginas());
            stmt.setBoolean(8, libro.isDisponible());

            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.err.println("Error al agregar libro: " + e.getMessage());
            return false;
        }
    }

    public LibroFisico buscarPorId(int id) {
        String sql = "SELECT * FROM LibrosFisicos WHERE id = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, id);
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    // rs.getDate(...).toLocalDate() -> LocalDate
                    return new LibroFisico(
                        rs.getInt("id"),
                        rs.getString("titulo"),
                        rs.getDate("fechaIngreso").toLocalDate(),
                        rs.getString("ubicacion"),
                        rs.getString("condicion"),
                        rs.getString("autor"),
                        rs.getInt("numPaginas")
                    );
                }
            }

        } catch (SQLException e) {
            System.err.println("Error buscando libro: " + e.getMessage());
        }
        return null;
    }

    public List<LibroFisico> obtenerTodos() {
        List<LibroFisico> lista = new ArrayList<>();
        String sql = "SELECT * FROM LibrosFisicos";

        try (Connection conn = DBConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                LibroFisico libro = new LibroFisico(
                    rs.getInt("id"),
                    rs.getString("titulo"),
                    rs.getDate("fechaIngreso").toLocalDate(),
                    rs.getString("ubicacion"),
                    rs.getString("condicion"),
                    rs.getString("autor"),
                    rs.getInt("numPaginas")
                );
                // si tu constructor no inicializa 'disponible', ponlo:
                libro.setDisponible(rs.getBoolean("disponible"));
                lista.add(libro);
            }

        } catch (SQLException e) {
            System.err.println("Error al obtener libros: " + e.getMessage());
        }

        return lista;
    }

    public boolean actualizarDisponibilidad(int idLibro, boolean disponible) {
        String sql = "UPDATE LibrosFisicos SET disponible = ? WHERE id = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setBoolean(1, disponible);
            stmt.setInt(2, idLibro);
            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.err.println("Error al actualizar disponibilidad: " + e.getMessage());
            return false;
        }
    }
}
//MAIN
import java.util.List;
import java.util.ArrayList;
import java.util.Scanner;
import java.time.LocalDate;

// Tus clases del proyecto
import sgbu.LibroFisico;
import sgbu.LibroFisicoDAO;
import sgbu.Biblioteca;
import sgbu.AlumnoDAO;
import sgbu.ArchivoUtil;

import java.util.List;
import java.util.Scanner;
import java.time.LocalDate;

import sgbu.UsuarioDAO;
import sgbu.RecursoDAO;
import sgbu.PrestamoDAO;
import sgbu.DevolucionDAO;

public class Main {

    public static final String VERDE = "\u001B[32m";
    public static final String ROJO = "\u001B[31m";
    public static final String AMARILLO = "\u001B[33m";
    public static final String AZUL = "\u001B[34m";
    public static final String RESET = "\u001B[0m";

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        UsuarioDAO usuarioDAO = new UsuarioDAO();
        RecursoDAO recursoDAO = new RecursoDAO();
        PrestamoDAO prestamoDAO = new PrestamoDAO();
        DevolucionDAO devolucionDAO = new DevolucionDAO();

        Biblioteca biblioteca = new Biblioteca(1, "Biblioteca UTP Central", "Av. Arequipa 1234");

        // ======================================================
        // 🔹 Cargar usuarios desde la BD
        // ======================================================
        List<Usuario> usuariosBD = usuarioDAO.listarTodos();
        usuariosBD.forEach(biblioteca::registrarUsuario);

        // 🔹 Cargar recursos desde la BD
        List<Recurso> recursosBD = recursoDAO.listarRecursos();
        recursosBD.forEach(biblioteca::registrarRecurso);

        // 🔹 Cargar préstamos desde la BD
        List<Prestamo> prestamosBD = prestamoDAO.listarPrestamos();
        prestamosBD.forEach(biblioteca::registrarPrestamoDesdeBD);

        int opcion;
        do {
            System.out.println(AZUL + "\n===== SISTEMA DE GESTIÓN DE BIBLIOTECA =====" + RESET);
            System.out.println("1. Gestión de Usuarios");
            System.out.println("2. Gestión de Recursos");
            System.out.println("3. Gestión de Préstamos");
            System.out.println("4. Mostrar todo");
            System.out.println("5. Salir");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine();

            switch (opcion) {
                case 1 -> menuUsuarios(sc, biblioteca, usuarioDAO);
                case 2 -> menuRecursos(sc, biblioteca, recursoDAO);
                case 3 -> menuPrestamos(sc, biblioteca, prestamoDAO, devolucionDAO);
                case 4 -> mostrarTodo(biblioteca);
                case 5 -> System.out.println(VERDE + "👋 Cerrando sistema..." + RESET);
                default -> System.out.println(ROJO + "❌ Opción inválida." + RESET);
            }

        } while (opcion != 5);

        sc.close();
    }

    // ============================================================
    // 🔹 MENÚ USUARIOS
    // ============================================================
    private static void menuUsuarios(Scanner sc, Biblioteca biblioteca, UsuarioDAO dao) {
        int op;
        do {
            System.out.println("\n--- GESTIÓN DE USUARIOS ---");
            System.out.println("1. Registrar usuario");
            System.out.println("2. Mostrar usuarios");
            System.out.println("3. Buscar usuario");
            System.out.println("4. Volver");
            System.out.print("Opción: ");
            op = sc.nextInt();
            sc.nextLine();

            switch (op) {
                case 1 -> registrarUsuario(sc, biblioteca, dao);
                case 2 -> biblioteca.mostrarUsuarios();
                case 3 -> buscarUsuario(sc, dao);
                case 4 -> {}
                default -> System.out.println("❌ Opción inválida");
            }

        } while (op != 4);
    }

    private static void registrarUsuario(Scanner sc, Biblioteca biblioteca, UsuarioDAO dao) {
        System.out.println("\n--- REGISTRAR USUARIO (Alumno/Profesor/Invitado/Bibliotecario) ---");

        System.out.print("Tipo (alumno/profesor/invitado/bibliotecario): ");
        String tipo = sc.nextLine().toLowerCase();

        System.out.print("ID: ");
        String id = sc.nextLine();
        System.out.print("Nombre: ");
        String nombre = sc.nextLine();
        System.out.print("Email: ");
        String email = sc.nextLine();
        System.out.print("Teléfono: ");
        String tel = sc.nextLine();
        System.out.print("Contraseña: ");
        String pass = sc.nextLine();

        Usuario u = null;

        switch (tipo) {
            case "alumno" -> {
                System.out.print("Carrera: ");
                String carrera = sc.nextLine();
                System.out.print("Código alumno: ");
                String codigo = sc.nextLine();
                System.out.print("Ciclo: ");
                int ciclo = sc.nextInt();
                sc.nextLine();

                u = new Alumno(id, nombre, email, tel, pass, carrera, codigo, ciclo);
            }

            case "profesor" -> {
                System.out.print("Facultad: ");
                String fac = sc.nextLine();
                System.out.print("Especialidad: ");
                String esp = sc.nextLine();
                System.out.print("Código profesor: ");
                String cod = sc.nextLine();

                u = new Profesor(id, nombre, email, tel, pass, fac, esp, cod);
            }

            case "invitado" -> {
                System.out.print("Tipo invitado: ");
                String tipoInv = sc.nextLine();
                System.out.print("Fecha expiración (AAAA-MM-DD): ");
                LocalDate fexp = LocalDate.parse(sc.nextLine());

                u = new Invitado(id, nombre, email, tel, pass, tipoInv, fexp);
            }

            case "bibliotecario" -> {
                System.out.print("Turno (mañana/tarde/noche): ");
                String turno = sc.nextLine();
                System.out.print("Especialidad: ");
                String especialidad = sc.nextLine();

                u = new Bibliotecario(id, nombre, email, tel, pass, turno, especialidad);
            }

            default -> {
                System.out.println(ROJO + "Tipo inválido." + RESET);
                return;
            }
        }

        dao.insertar(u);         // BD
        biblioteca.registrarUsuario(u);  // Memoria

        System.out.println(VERDE + "Usuario registrado correctamente." + RESET);
    }

    private static void buscarUsuario(Scanner sc, UsuarioDAO dao) {
        System.out.print("Ingrese ID del usuario: ");
        Usuario u = dao.buscarPorId(sc.nextLine());

        if (u != null)
            System.out.println(VERDE + "Encontrado: " + u.consultarDetalles() + RESET);
        else
            System.out.println(ROJO + "No encontrado." + RESET);
    }

    // ============================================================
    // 🔹 MENÚ RECURSOS
    // ============================================================
    private static void menuRecursos(Scanner sc, Biblioteca biblioteca, RecursoDAO dao) {
        int op;
        do {
            System.out.println("\n--- GESTIÓN DE RECURSOS ---");
            System.out.println("1. Registrar recurso");
            System.out.println("2. Mostrar recursos");
            System.out.println("3. Buscar recurso");
            System.out.println("4. Eliminar recurso");
            System.out.println("5. Volver");
            System.out.print("Opción: ");
            op = sc.nextInt();
            sc.nextLine();

            switch (op) {
                case 1 -> registrarRecurso(sc, biblioteca, dao);
                case 2 -> biblioteca.mostrarRecursos();
                case 3 -> buscarRecurso(sc, dao);
                case 4 -> eliminarRecurso(sc, dao, biblioteca);
                case 5 -> {}
                default -> System.out.println("❌ Opción inválida");
            }

        } while (op != 5);
    }

    private static void registrarRecurso(Scanner sc, Biblioteca biblioteca, RecursoDAO dao) {
        System.out.println("\n--- REGISTRAR RECURSO ---");
        System.out.print("Tipo (libro_fisico / revista / libro_digital): ");
        String tipo = sc.nextLine().toLowerCase();

        System.out.print("ID: ");
        int id = sc.nextInt();
        sc.nextLine();
        System.out.print("Título: ");
        String titulo = sc.nextLine();

        Recurso r = null;

        switch (tipo) {
            case "libro_fisico" -> {
                System.out.print("Ubicación: ");
                String ub = sc.nextLine();
                System.out.print("Condición: ");
                String cond = sc.nextLine();
                System.out.print("Autor: ");
                String autor = sc.nextLine();
                System.out.print("Páginas: ");
                int pag = sc.nextInt();
                sc.nextLine();

                r = new LibroFisico(id, titulo, LocalDate.now(), ub, cond, autor, pag);
            }

            case "revista" -> {
                System.out.print("Ubicación: ");
                String ub = sc.nextLine();
                System.out.print("Condición: ");
                String cond = sc.nextLine();
                System.out.print("Volumen: ");
                int vol = sc.nextInt();
                System.out.print("Número: ");
                int num = sc.nextInt();
                sc.nextLine();

                r = new Revista(id, titulo, LocalDate.now(), ub, cond, vol, num);
            }

            case "libro_digital" -> {
                System.out.print("Formato: ");
                String formato = sc.nextLine();
                System.out.print("URL acceso: ");
                String url = sc.nextLine();
                System.out.print("Autor: ");
                String autor = sc.nextLine();
                System.out.print("Páginas: ");
                int pags = sc.nextInt();
                sc.nextLine();

                r = new LibroDigital(id, titulo, LocalDate.now(), formato, url, autor, pags);
            }

            default -> {
                System.out.println(ROJO + "Tipo inválido." + RESET);
                return;
            }
        }

        dao.insertarRecurso(r);
        biblioteca.registrarRecurso(r);

        System.out.println(VERDE + "Recurso registrado correctamente." + RESET);
    }

    private static void buscarRecurso(Scanner sc, RecursoDAO dao) {
        System.out.print("ID del recurso: ");
        Recurso r = dao.buscarRecurso(sc.nextInt());
        sc.nextLine();

        if (r != null)
            System.out.println(VERDE + r.consultarDetalles() + RESET);
        else
            System.out.println(ROJO + "No encontrado." + RESET);
    }

    private static void eliminarRecurso(Scanner sc, RecursoDAO dao, Biblioteca b) {
        System.out.print("ID del recurso a eliminar: ");
        int id = sc.nextInt();
        sc.nextLine();

        if (dao.eliminarRecurso(id)) {
            b.eliminarRecurso(id);
            System.out.println(VERDE + "Recurso eliminado." + RESET);
        } else {
            System.out.println(ROJO + "No se pudo eliminar." + RESET);
        }
    }

    // ============================================================
    // 🔹 MENÚ PRÉSTAMOS
    // ============================================================
    private static void menuPrestamos(Scanner sc, Biblioteca biblioteca, PrestamoDAO prestamoDAO, DevolucionDAO devolucionDAO) {
        int op;
        do {
            System.out.println("\n--- GESTIÓN DE PRÉSTAMOS ---");
            System.out.println("1. Registrar préstamo");
            System.out.println("2. Registrar devolución");
            System.out.println("3. Mostrar préstamos");
            System.out.println("4. Volver");
            System.out.print("Opción: ");
            op = sc.nextInt();
            sc.nextLine();

            switch (op) {
                case 1 -> registrarPrestamo(sc, biblioteca, prestamoDAO);
                case 2 -> registrarDevolucion(sc, biblioteca, devolucionDAO);
                case 3 -> biblioteca.mostrarPrestamos();
                case 4 -> {}
                default -> System.out.println("❌ Opción inválida");
            }

        } while (op != 4);
    }

    private static void registrarPrestamo(Scanner sc, Biblioteca b, PrestamoDAO dao) {
        b.mostrarUsuarios();
        System.out.print("ID usuario: ");
        String idUser = sc.nextLine();

        b.mostrarRecursos();
        System.out.print("ID recurso: ");
        int idRecurso = sc.nextInt();
        sc.nextLine();

        Usuario u = b.buscarUsuarioPorId(idUser);
        Recurso r = b.buscarRecursoPorId(idRecurso);

        if (u == null || r == null || !r.isDisponible()) {
            System.out.println(ROJO + "No se puede procesar el préstamo." + RESET);
            return;
        }

        Prestamo p = b.registrarPrestamo(u, r);
        dao.registrarPrestamo(p);

        System.out.println(VERDE + "Préstamo realizado." + RESET);
    }

    private static void registrarDevolucion(Scanner sc, Biblioteca b, DevolucionDAO dao) {
        b.mostrarPrestamos();

        System.out.print("ID del préstamo devuelto: ");
        int idPrestamo = sc.nextInt();
        sc.nextLine();

        Prestamo p = b.buscarPrestamoPorId(idPrestamo);

        if (p == null) {
            System.out.println(ROJO + "Préstamo no encontrado." + RESET);
            return;
        }

        Devolucion d = b.registrarDevolucion(p);
        dao.insertar(d);

        System.out.println(VERDE + "Devolución registrada." + RESET);
    }

    // ============================================================
    // 🔹 MOSTRAR TODO
    // ============================================================
    private static void mostrarTodo(Biblioteca b) {
        b.mostrarUsuarios();
        b.mostrarRecursos();
        b.mostrarPrestamos();
    }
}
//Microscopio
import java.time.LocalDate;

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
//Multimetro
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
//Penalización
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
//Prestamo
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
    public void setIdPrestamo(int idPrestamo) {
    this.idPrestamo = idPrestamo;
}

    public void setEstado(String estado) {
    this.estado = estado;
}
    public void setUsuario(Usuario usuario) {
    this.usuario = usuario;
}

    public void setRecurso(Recurso recurso) {
    this.recurso = recurso;
}

}
//PrestamoDAO
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import sgbu.DBConnection;
import java.time.LocalDate;
import sgbu.Prestamo;
import sgbu.Usuario;
import sgbu.Recurso;

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

import sgbu.Prestamo;
import sgbu.Alumno;
import sgbu.LibroFisico;
import java.time.LocalDate;

public class PrestamoDAO {

    private final AlumnoDAO alumnoDAO = new AlumnoDAO();
    private final LibroFisicoDAO libroDAO = new LibroFisicoDAO();

    // Registrar un préstamo (usa Usuario y Recurso ya vinculados en el objeto Prestamo)
    public void registrarPrestamo(Prestamo p) {
        String sql = "INSERT INTO Prestamos (idUsuario, idRecurso, fechaInicio, fechaDevolucion, estado) VALUES (?, ?, ?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            if (p.getUsuario() == null || p.getRecurso() == null) {
                System.out.println("❌ Usuario o recurso nulo en el préstamo.");
                return;
            }

            stmt.setString(1, p.getUsuario().getIdUsuario());
            stmt.setInt(2, p.getRecurso().getId());
            stmt.setDate(3, java.sql.Date.valueOf(p.getFechaInicio()));
            stmt.setDate(4, java.sql.Date.valueOf(p.getFechaDevolucion()));
            stmt.setString(5, p.getEstado());

            stmt.executeUpdate();
            System.out.println("📘 Préstamo registrado correctamente.");

            // opcional: si quieres recuperar el id autogenerado y asignarlo al objeto p:
            // try (ResultSet keys = stmt.getGeneratedKeys()) { if (keys.next()) p.setIdPrestamo(keys.getInt(1)); }

        } catch (SQLException e) {
            System.out.println("❌ Error al registrar préstamo: " + e.getMessage());
        }
    }

    // Listar todos los préstamos construyendo Prestamo con Usuario y Recurso
    public List<Prestamo> listarPrestamos() {
        List<Prestamo> lista = new ArrayList<>();
        String sql = "SELECT * FROM Prestamos";

        try (Connection conn = DBConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                int idPrestamo = rs.getInt("idPrestamo");
                String idUsuario = rs.getString("idUsuario");
                int idRecurso = rs.getInt("idRecurso");
                LocalDate fechaInicio = rs.getDate("fechaInicio").toLocalDate();
                LocalDate fechaDevolucion = rs.getDate("fechaDevolucion").toLocalDate();
                String estado = rs.getString("estado");

                // 1) obtener objetos completos desde sus DAOs
                Alumno usuarioObj = alumnoDAO.buscarAlumnoPorId(idUsuario); // devuelve Alumno o null
                LibroFisico recursoObj = libroDAO.buscarPorId(idRecurso);   // devuelve LibroFisico o null

                // 2) crear Prestamo sólo si ambos objetos existen (evita NPE)
                if (usuarioObj != null && recursoObj != null) {
                    Prestamo p = new Prestamo(usuarioObj, recursoObj, fechaInicio, fechaDevolucion);
                    p.setIdPrestamo(idPrestamo);
                    p.setEstado(estado);
                    lista.add(p);
                } else {
                    // Si faltan datos en BD, puedes crear un Prestamo "parcial" o registrar el incidente:
                    System.out.println("⚠️ Préstamo " + idPrestamo + " tiene usuario/recurso no encontrados (usuario="
                                        + idUsuario + ", recurso=" + idRecurso + ")");
                }
            }

        } catch (SQLException e) {
            System.out.println("❌ Error al listar préstamos: " + e.getMessage());
        }

        return lista;
    }

    // Buscar préstamo por id (igualmente construye Prestamo con Usuario y Recurso)
    public Prestamo buscarPrestamoPorId(int idBuscado) {
        String sql = "SELECT * FROM Prestamos WHERE idPrestamo = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, idBuscado);
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    String idUsuario = rs.getString("idUsuario");
                    int idRecurso = rs.getInt("idRecurso");
                    LocalDate fechaInicio = rs.getDate("fechaInicio").toLocalDate();
                    LocalDate fechaDevolucion = rs.getDate("fechaDevolucion").toLocalDate();
                    String estado = rs.getString("estado");

                    Alumno usuarioObj = alumnoDAO.buscarAlumnoPorId(idUsuario);
                    LibroFisico recursoObj = libroDAO.buscarPorId(idRecurso);

                    if (usuarioObj != null && recursoObj != null) {
                        Prestamo p = new Prestamo(usuarioObj, recursoObj, fechaInicio, fechaDevolucion);
                        p.setIdPrestamo(idBuscado);
                        p.setEstado(estado);
                        return p;
                    } else {
                        System.out.println("⚠️ Prestamo encontrado pero falta usuario o recurso en BD.");
                    }
                }
            }

        } catch (SQLException e) {
            System.out.println("❌ Error al buscar préstamo: " + e.getMessage());
        }

        return null;
    }

    // Actualizar estado (por ejemplo marcar 'devuelto')
    public boolean actualizarEstado(int idPrestamo, String nuevoEstado) {
        String sql = "UPDATE Prestamos SET estado = ? WHERE idPrestamo = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, nuevoEstado);
            stmt.setInt(2, idPrestamo);
            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.out.println("❌ Error al actualizar préstamo: " + e.getMessage());
            return false;
        }
    }

    // Eliminar préstamo
    public boolean eliminarPrestamo(int idPrestamo) {
        String sql = "DELETE FROM Prestamos WHERE idPrestamo = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, idPrestamo);
            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.out.println("❌ Error al eliminar préstamo: " + e.getMessage());
            return false;
        }
    }
}
//Profesor
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
    public String getFacultad() {
        return facultad;
}

    public String getEspecialidad() {
        return especialidad;
}

    public String getCodigoProfesor() {
        return codigoProfesor;
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
    @Override
    public String consultarDetalles() {
    return String.format("Profesor[id=%s, nombre=%s, facultad=%s, especialidad=%s]",
            idUsuario, nombre, facultad, especialidad);
}

}
//Proyector
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

//Recurso
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
//RecursoDAO
import sgbu.*;
import sgbu.DBConnection;

import java.sql.*;
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

public class RecursoDAO {

    // ----------------------------------------------------------
    // 🔹 INSERTAR RECURSO (detecta tipo automáticamente)
    // ----------------------------------------------------------
    public void insertarRecurso(Recurso recurso) {
        String sql = "INSERT INTO Recursos (" +
                "id, titulo, fechaIngreso, disponible, tipo, " +
                "autor, paginas, ubicacion, condicion, formato, urlAcceso, volumen, numero" +
                ") VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, recurso.getId());
            stmt.setString(2, recurso.getTitulo());
            stmt.setDate(3, Date.valueOf(recurso.getFechaIngreso()));
            stmt.setBoolean(4, recurso.isDisponible());

            // ----------------------------
            // ¿Qué tipo es?
            // ----------------------------
            if (recurso instanceof LibroFisico lf) {
                stmt.setString(5, "LIBRO_FISICO");
                stmt.setString(6, lf.getAutor());
                stmt.setInt(7, lf.getNumPaginas());
                stmt.setString(8, lf.getUbicacionEstante());
                stmt.setString(9, lf.getCondicion());

                stmt.setString(10, null);
                stmt.setString(11, null);
                stmt.setNull(12, Types.INTEGER);
                stmt.setNull(13, Types.INTEGER);

            } else if (recurso instanceof Revista rv) {
                stmt.setString(5, "REVISTA");
                stmt.setString(6, null);
                stmt.setNull(7, Types.INTEGER);
                stmt.setString(8, rv.getUbicacionEstante());
                stmt.setString(9, rv.getCondicion());

                stmt.setString(10, null);
                stmt.setString(11, null);
                stmt.setInt(12, rv.getVolumen());
                stmt.setInt(13, rv.getNumero());

            } else if (recurso instanceof LibroDigital ld) {
                stmt.setString(5, "LIBRO_DIGITAL");
                stmt.setString(6, ld.getAutor());
                stmt.setInt(7, ld.getNumPaginas());

                stmt.setString(8, null);
                stmt.setString(9, null);

                stmt.setString(10, ld.getFormato());
                stmt.setString(11, ld.getUrlAcceso());
                stmt.setNull(12, Types.INTEGER);
                stmt.setNull(13, Types.INTEGER);

            } else {
                throw new RuntimeException("Tipo de recurso no reconocido");
            }

            stmt.executeUpdate();

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // ----------------------------------------------------------
    // 🔹 LISTAR TODOS LOS RECURSOS (crea objetos correctos)
    // ----------------------------------------------------------
    public List<Recurso> listarRecursos() {
        List<Recurso> lista = new ArrayList<>();
        String sql = "SELECT * FROM Recursos";

        try (Connection conn = DBConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {

                String tipo = rs.getString("tipo");
                int id = rs.getInt("id");
                String titulo = rs.getString("titulo");
                LocalDate fecha = rs.getDate("fechaIngreso").toLocalDate();
                boolean disp = rs.getBoolean("disponible");

                Recurso r = null;

                switch (tipo) {

                    case "LIBRO_FISICO" -> {
                        r = new LibroFisico(
                                id,
                                titulo,
                                fecha,
                                rs.getString("ubicacion"),
                                rs.getString("condicion"),
                                rs.getString("autor"),
                                rs.getInt("paginas")
                        );
                    }

                    case "REVISTA" -> {
                        r = new Revista(
                                id,
                                titulo,
                                fecha,
                                rs.getString("ubicacion"),
                                rs.getString("condicion"),
                                rs.getInt("volumen"),
                                rs.getInt("numero")
                        );
                    }

                    case "LIBRO_DIGITAL" -> {
                        r = new LibroDigital(
                                id,
                                titulo,
                                fecha,
                                rs.getString("formato"),
                                rs.getString("urlAcceso"),
                                rs.getString("autor"),
                                rs.getInt("paginas")
                        );
                    }
                }

                if (r != null) {
                    r.setDisponible(disp);
                    lista.add(r);
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return lista;
    }

    // ----------------------------------------------------------
    // 🔹 BUSCAR POR ID
    // ----------------------------------------------------------
    public Recurso buscarRecurso(int idBuscado) {
        return listarRecursos()
                .stream()
                .filter(r -> r.getId() == idBuscado)
                .findFirst()
                .orElse(null);
    }

    // ----------------------------------------------------------
    // 🔹 ELIMINAR RECURSO
    // ----------------------------------------------------------
    public boolean eliminarRecurso(int id) {
        String sql = "DELETE FROM Recursos WHERE id = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, id);
            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }
}
//RecursoDigital
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
//RecursoFisico
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
//Reservable
public interface Reservable {
    boolean reservar(String quien, java.time.LocalDate fechaInicio, java.time.LocalDate fechaFin);
    void liberar();
    boolean estaReservado();
}

//Revista
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
    public int getVolumen() { return volumen; }
    public int getNumero() { return numero; }
    
    public List<String> consultarArticulos() {
        return Arrays.asList("Artículo 1", "Artículo 2");
    }

    @Override
    public String consultarDetalles() {
        return String.format("Revista[id=%d,titulo=%s,vol=%d,num=%d,disp=%b]", id, titulo, volumen, numero, disponible);
    }
}
//RevistaDAO
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class RevistaDAO {

    public void insertarRevista(Revista r) {
        String sql = "INSERT INTO Revistas (id, titulo, fechaIngreso, ubicacion, condicion, volumen, numero) VALUES (?, ?, ?, ?, ?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setInt(1, r.getId());
            stmt.setString(2, r.getTitulo());
            stmt.setDate(3, java.sql.Date.valueOf(r.getFechaIngreso()));
            stmt.setString(4, r.getUbicacionEstante());
            stmt.setString(5, r.getCondicion());
            stmt.setInt(6, r.getVolumen());
            stmt.setInt(7, r.getNumero());

            stmt.executeUpdate();
            System.out.println("Revista registrada.");

        } catch (SQLException e) {
            System.out.println("Error insertando revista: " + e.getMessage());
        }
    }

    public List<Revista> listarRevistas() {
        List<Revista> lista = new ArrayList<>();
        String sql = "SELECT * FROM Revistas";

        try (Connection conn = DBConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                Revista rv = new Revista(
                        rs.getInt("id"),
                        rs.getString("titulo"),
                        rs.getDate("fechaIngreso").toLocalDate(),
                        rs.getString("ubicacion"),
                        rs.getString("condicion"),
                        rs.getInt("volumen"),
                        rs.getInt("numero")
                );
                lista.add(rv);
            }

        } catch (SQLException e) {
            System.out.println("Error listando revistas: " + e.getMessage());
        }

        return lista;
    }
}
//SalaEstudio
import java.time.LocalDate;

public class SalaEstudio extends Ambiente {
    private int numMesas;

    // Constructor
    public SalaEstudio(int id, int capacidad, int numMesas) {
        super(id, "Sala de Estudio", LocalDate.now(), "Ubicación X", "Buena", capacidad);
        this.numMesas = numMesas;
    }

    public int getNumMesas() {
        return numMesas;
    }

    public boolean consultarDisponibilidadMesa() {
        return numMesas > 0;
    }

    @Override
    public String consultarDetalles() {
        return String.format("SalaEstudio[id=%d, titulo=%s, capacidad=%d, numMesas=%d]",
                id, titulo, capacidad, numMesas);
    }
}
//SalaLectura
import java.time.LocalDate;

public class SalaLectura extends Ambiente {
    private int numSillas;

    public SalaLectura(int id, int capacidad, int numSillas) {
        super(id, "Sala de Lectura", LocalDate.now(), "Ubicación X", "Buena", capacidad);
        this.numSillas = numSillas;
    }

    public int getNumSillas() {
        return numSillas; // Este getter es obligatorio para usar en el DAO
    }

    @Override
    public String consultarDetalles() {
        return String.format("SalaLectura[id=%d, titulo=%s, capacidad=%d, numSillas=%d]",
                id, titulo, capacidad, numSillas);
    }
}

//Tablet
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

//TesisDigital
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
//TestConexion
public class TestConexion {
    public static void main(String[] args) {
        try {
            System.load("C:\\Program Files\\Java\\jdk-21\\bin\\mssql-jdbc_auth-13.2.1.x64.dll");
            Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

            String url = "jdbc:sqlserver://localhost:1433;"
                       + "databaseName=DB_Biblioteca;"
                       + "encrypt=false;"
                       + "integratedSecurity=true;";

            java.sql.Connection conn = java.sql.DriverManager.getConnection(url);
            System.out.println("✅ Conectado correctamente a DB_Biblioteca.");
            conn.close();

        } catch (Exception e) {
            System.out.println("❌ Error de conexión: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
//Usuario
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
    public String getPassword(){return password;}
    public String getTelefono(){return telefono;}
    
    public String consultarDetalles() {
    return String.format("Usuario[id=%s, nombre=%s, email=%s, telefono=%s]",
            idUsuario, nombre, email, telefono);
}


    // Acciones delegadas a Prestamo/Biblioteca
    public abstract boolean reservarRecurso(int recursoId, Biblioteca biblioteca);
    public abstract java.util.List<Prestamo> verHistorialPrestamos(Biblioteca biblioteca);

    @Override
    public String toString() {
        return String.format("Usuario[id=%d,nombre=%s,email=%s]", idUsuario, nombre, email);
    }
}
//UsuarioDAO
import java.sql.*;
import java.util.ArrayList;
import java.util.List;


import sgbu.*;
import sgbu.DBConnection;

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class UsuarioDAO {

    // ============================================================
    // INSERTAR USUARIO
    // ============================================================
    public boolean insertar(Usuario u) {
        String sql = "INSERT INTO Usuarios (idUsuario, nombre, email, telefono, password, tipoUsuario, " +
                "carrera, ciclo, codigoAlumno, facultad, especialidad, codigoProfesor, " +
                "tipoInvitado, fechaExpiracion, turno) " +
                "VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, u.getIdUsuario());
            stmt.setString(2, u.getNombre());
            stmt.setString(3, u.getEmail());
            stmt.setString(4, u.getTelefono());
            stmt.setString(5, u.getPassword());

            // =============================
            // IDENTIFICAR SUBTIPO
            // =============================
            if (u instanceof Alumno) {
                Alumno a = (Alumno) u;
                stmt.setString(6, "alumno");

                stmt.setString(7, a.getCarrera());
                stmt.setInt(8, a.getCiclo());
                stmt.setString(9, a.getCodigoAlumno());

                stmt.setNull(10, Types.VARCHAR);
                stmt.setNull(11, Types.VARCHAR);
                stmt.setNull(12, Types.VARCHAR);
                stmt.setNull(13, Types.VARCHAR);
                stmt.setNull(14, Types.DATE);
                stmt.setNull(15, Types.VARCHAR);

            } else if (u instanceof Profesor) {
                Profesor p = (Profesor) u;
                stmt.setString(6, "profesor");

                stmt.setNull(7, Types.VARCHAR);
                stmt.setNull(8, Types.INTEGER);
                stmt.setNull(9, Types.VARCHAR);

                stmt.setString(10, p.getFacultad());
                stmt.setString(11, p.getEspecialidad());
                stmt.setString(12, p.getCodigoProfesor());

                stmt.setNull(13, Types.VARCHAR);
                stmt.setNull(14, Types.DATE);
                stmt.setNull(15, Types.VARCHAR);

            } else if (u instanceof Invitado) {
                Invitado i = (Invitado) u;
                stmt.setString(6, "invitado");

                stmt.setNull(7, Types.VARCHAR);
                stmt.setNull(8, Types.INTEGER);
                stmt.setNull(9, Types.VARCHAR);
                stmt.setNull(10, Types.VARCHAR);
                stmt.setNull(11, Types.VARCHAR);
                stmt.setNull(12, Types.VARCHAR);

                stmt.setString(13, i.getTipoInvitado());
                stmt.setDate(14, Date.valueOf(i.getFechaExpiracion()));

                stmt.setNull(15, Types.VARCHAR);

            } else if (u instanceof Bibliotecario) {
                Bibliotecario b = (Bibliotecario) u;
                stmt.setString(6, "bibliotecario");

                stmt.setNull(7, Types.VARCHAR);
                stmt.setNull(8, Types.INTEGER);
                stmt.setNull(9, Types.VARCHAR);
                stmt.setNull(10, Types.VARCHAR);
                stmt.setNull(11, Types.VARCHAR);
                stmt.setNull(12, Types.VARCHAR);
                stmt.setNull(13, Types.VARCHAR);
                stmt.setNull(14, Types.DATE);

                stmt.setString(15, b.getTurno());
            }

            return stmt.executeUpdate() > 0;

        } catch (SQLException e) {
            System.out.println("Error al insertar usuario: " + e.getMessage());
            return false;
        }
    }

    // ============================================================
    // OBTENER TODOS LOS USUARIOS
    // ============================================================
    public List<Usuario> listarTodos() {
        List<Usuario> lista = new ArrayList<>();
        String sql = "SELECT * FROM Usuarios";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql);
             ResultSet rs = stmt.executeQuery()) {

            while (rs.next()) {
                lista.add(mapUsuario(rs));
            }

        } catch (SQLException e) {
            System.out.println("Error al listar usuarios: " + e.getMessage());
        }

        return lista;
    }

    // ============================================================
    // BUSCAR POR ID
    // ============================================================
    public Usuario buscarPorId(String idUsuario) {
        String sql = "SELECT * FROM Usuarios WHERE idUsuario = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, idUsuario);
            ResultSet rs = stmt.executeQuery();

            if (rs.next()) {
                return mapUsuario(rs);
            }

        } catch (SQLException e) {
            System.out.println("Error al buscar usuario: " + e.getMessage());
        }

        return null;
    }

    // ============================================================
    // MAPEO DE RESULTSET → OBJETO DEL SUBTIPO CORRECTO
    // ============================================================
    private Usuario mapUsuario(ResultSet rs) throws SQLException {

    String tipo = rs.getString("tipoUsuario");

    switch (tipo.toLowerCase()) {

        case "alumno":
            return new Alumno(
                    rs.getString("idUsuario"),
                    rs.getString("nombre"),
                    rs.getString("email"),
                    rs.getString("telefono"),
                    rs.getString("password"),
                    rs.getString("carrera"),
                    rs.getString("codigoAlumno"),
                    rs.getInt("ciclo")
            );

        case "profesor":
            return new Profesor(
                    rs.getString("idUsuario"),
                    rs.getString("nombre"),
                    rs.getString("email"),
                    rs.getString("telefono"),
                    rs.getString("password"),
                    rs.getString("facultad"),
                    rs.getString("especialidad"),
                    rs.getString("codigoProfesor")
            );

        case "invitado":
            return new Invitado(
                    rs.getString("idUsuario"),
                    rs.getString("nombre"),
                    rs.getString("email"),
                    rs.getString("telefono"),
                    rs.getString("password"),
                    rs.getString("tipoInvitado"),
                    rs.getDate("fechaExpiracion").toLocalDate()
            );

        case "bibliotecario":
            return new Bibliotecario(
                    rs.getString("idUsuario"),
                    rs.getString("nombre"),
                    rs.getString("email"),
                    rs.getString("telefono"),
                    rs.getString("password"),
                    rs.getString("turno"), 
                    rs.getString("especialidad")// tu clase solo usa turno
            );

        default:
            throw new SQLException("Tipo de usuario desconocido: " + tipo);
    }
}

}


