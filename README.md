package sgbu;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.List;
import java.util.Scanner;
import java.time.LocalDate;
import sgbu.PrestamoDAO;

// MAIN ESTA ACTUALIZADO YA QUE SE AUMENTO METODO MOSTRAR TODO Y DEVOLVER  PRESTAMOS + BASE DE DATOS + PENALIZACION

public class Main {

    //  Colores para consola
    public static final String VERDE = "\u001B[32m";
    public static final String ROJO = "\u001B[31m";
    public static final String AMARILLO = "\u001B[33m";
    public static final String AZUL = "\u001B[34m";
    public static final String RESET = "\u001B[0m";

    public static void main(String[] args) throws SQLException {

        //  Inicializar objetos principales
        Scanner sc = new Scanner(System.in);
        AlumnoDAO alumnoDAO = new AlumnoDAO();

        LibroFisicoDAO libroDAO = new LibroFisicoDAO();
        Biblioteca biblioteca = new Biblioteca(1, "Biblioteca UTP Central", "Av. Arequipa ");
        PrestamoDAO prestamoDAO = new PrestamoDAO();

        //  Cargar alumnos desde la base de datos
        List<Alumno> alumnosBD = alumnoDAO.listarAlumnos();
        for (Alumno a : alumnosBD) {
            biblioteca.registrarUsuario(a);
        }

        //  Cargar libros desde la base de datos
         List<LibroFisico> librosBD = libroDAO.obtenerLibros();
        for (LibroFisico l : librosBD) {
            biblioteca.registrarRecurso(l);

            //  Cargar préstamos desde la base de datos
            List<Prestamo> prestamosBD = prestamoDAO.obtenerPrestamos();
            for (Prestamo p : prestamosBD) {
                biblioteca.registrarPrestamo(p.getUsuario(), p.getRecurso());
            }

        }

        //  Cargar usuarios desde archivo (SOLO UNA VEZ)
        //  Cargar usuarios desde archivo (sin duplicados y sincronizando con BD)
        //ArchivoUtil.cargarAlumnos(biblioteca, "usuarios.txt");

        int opcionPrincipal;
        do {
            System.out.println(AZUL + "\n===== SISTEMA DE GESTIÓN DE BIBLIOTECA =====" + RESET);
            System.out.println("1. Gestión de Usuarios");
            System.out.println("2. Gestión de Recursos");
            System.out.println("3. Gestión de Préstamos");
            System.out.println("4. Mostrar todos los datos");
            System.out.println("5. Guardar y salir");
            System.out.print("Seleccione una opción: ");
            opcionPrincipal = sc.nextInt();
            sc.nextLine();// limpiar buffer

            switch (opcionPrincipal) {
                case 1 -> menuUsuarios(sc, biblioteca, alumnoDAO);
                case 2 -> menuRecursos(sc, biblioteca);
                case 3 -> menuPrestamos(sc, biblioteca,prestamoDAO);
                case 4 -> mostrarTodo(biblioteca);
                case 5 -> {
                    ArchivoUtil.guardarAlumnos(biblioteca, "usuarios.txt");
                    System.out.println(VERDE + " Cambios guardados. Cerrando sistema..." + RESET);
                }
                default -> System.out.println(ROJO + " Opción inválida. Intente nuevamente." + RESET);
            }
        } while (opcionPrincipal != 5);

        sc.close();
    }

    // ===========================================================
    //                     SUBMENÚ USUARIOS
    // ===========================================================
    private static void menuUsuarios(Scanner sc, Biblioteca biblioteca, AlumnoDAO dao) throws SQLException {
        int opcion;
        do {
            System.out.println("\n--- GESTIÓN DE USUARIOS  ---");
            System.out.println("1. Registrar usuario ");
            System.out.println("2. Mostrar usuarios");
            System.out.println("3. Eliminar usuario");
            System.out.println("4. Volver al menú principal");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine();

            switch (opcion) {
                case 1 -> registrarUsuario(sc, biblioteca, dao);
                case 2 -> mostrarUsuarios(dao);
                case 3 -> eliminarUsuario(sc, dao);
                case 4 -> System.out.println("↩ Volviendo al menú principal...");
                default -> System.out.println(" Opción inválida.");
            }
        } while (opcion != 4);
    }

    private static void registrarUsuario(Scanner sc, Biblioteca biblioteca, AlumnoDAO dao) {
        System.out.println("\n--- REGISTRAR NUEVO ALUMNO ---");
        System.out.print("ID Usuario: ");
        String id = sc.nextLine();
        System.out.print("Nombre: ");
        String nombre = sc.nextLine();
        System.out.print("Email: ");
        String email = sc.nextLine();
        System.out.print("Teléfono: ");
        String tel = sc.nextLine();
        System.out.print("Contraseña: ");
        String pass = sc.nextLine();
        System.out.print("Carrera: ");
        String carrera = sc.nextLine();
        System.out.print("Código de alumno: ");
        String codigo = sc.nextLine();
        System.out.print("Ciclo: ");
        int ciclo = sc.nextInt();
        sc.nextLine();

        Alumno a = new Alumno(id, nombre, email, tel, pass, carrera, codigo, ciclo);
        dao.agregarAlumno(a);
        biblioteca.registrarUsuario(a);

        System.out.println(VERDE + " Alumno registrado correctamente." + RESET);
    }

    private static void mostrarUsuarios(AlumnoDAO dao) {
        System.out.println("\n--- LISTA DE ALUMNOS REGISTRADOS ---");
        List<Alumno> alumnos = dao.listarAlumnos();
        if (alumnos.isEmpty()) {
            System.out.println(" No hay alumnos registrados.");
        } else {
            for (Alumno a : alumnos) {
                System.out.println("ID: " + a.getIdUsuario() +
                        " | Nombre: " + a.getNombre() +
                        " | Carrera: " + a.getCarrera() +
                        " | Ciclo: " + a.getCiclo());
            }
        }
    }

    private static void eliminarUsuario(Scanner sc, AlumnoDAO dao) throws SQLException {
        System.out.println("\n--- ELIMINAR ALUMNO ---");
        System.out.print("Ingrese el ID del alumno a eliminar: ");
        String id = sc.nextLine();

        Alumno alumno = dao.buscarAlumnoPorId(id);
        if (alumno == null) {
            System.out.println(ROJO + " No se encontró el alumno con ese ID." + RESET);
        } else {
            dao.eliminarAlumno(id);
            System.out.println(VERDE + "🗑 Alumno eliminado correctamente." + RESET);
        }
    }

    // ===========================================================
    //                       SUBMENÚ RECURSOS
    // ===========================================================
    private static void menuRecursos(Scanner sc, Biblioteca biblioteca) {
        int opcion;
        do {
            System.out.println("\n--- GESTIÓN DE RECURSOS ---");
            System.out.println("1. Registrar libro físico");
            System.out.println("2. Mostrar recursos");
            System.out.println("3. Eliminar recurso");
            System.out.println("4. Volver al menú principal");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine();

            switch (opcion) {
                case 1 -> registrarLibro(sc, biblioteca);
                case 2 -> biblioteca.mostrarRecursos();
                case 3 -> eliminarRecurso(sc, biblioteca);
                case 4 -> System.out.println("↩ Volviendo al menú principal...");
                default -> System.out.println(" Opción inválida.");
            }
        } while (opcion != 4);
    }

    private static void registrarLibro(Scanner sc, Biblioteca biblioteca) {
        System.out.print("ID: ");
        int id = sc.nextInt();
        sc.nextLine();
        System.out.print("Título: ");
        String titulo = sc.nextLine();
        System.out.print("Ubicación: ");
        String ubicacion = sc.nextLine();
        System.out.print("Condición: ");
        String condicion = sc.nextLine();
        System.out.print("Autor: ");
        String autor = sc.nextLine();
        System.out.print("Número de páginas: ");
        int paginas = sc.nextInt();
        sc.nextLine();

        LibroFisico libro = new LibroFisico(id, titulo, LocalDate.now(), ubicacion, condicion, autor, paginas);
        biblioteca.registrarRecurso(libro);
        System.out.println(VERDE + " Libro registrado correctamente." + RESET);
    }

    private static void eliminarRecurso(Scanner sc, Biblioteca biblioteca) {
        biblioteca.mostrarRecursos();
        System.out.print("Ingrese ID del recurso a eliminar: ");
        int id = sc.nextInt();
        sc.nextLine();
        boolean eliminado = biblioteca.eliminarRecurso(id);
        if (eliminado)
            System.out.println(VERDE + "🗑 Recurso eliminado." + RESET);
        else
            System.out.println(ROJO + " No se encontró el recurso." + RESET);
    }

    // ===========================================================
    //               SUBMENÚ PRÉSTAMOS
    // ===========================================================
    private static void menuPrestamos(Scanner sc, Biblioteca biblioteca,PrestamoDAO prestamoDAO) {
        int opcion;
        do {
            System.out.println("\n--- GESTIÓN DE PRÉSTAMOS ---");
            System.out.println("1. Registrar préstamo");
            System.out.println("2. Devolver préstamo");
            System.out.println("3. Mostrar préstamos");
            System.out.println("4. Volver al menú principal");
            System.out.print("Seleccione una opción: ");
            opcion = sc.nextInt();
            sc.nextLine();

            switch (opcion) {
                case 1 -> registrarPrestamo(sc, biblioteca);
                case 2 -> devolverPrestamo(sc, biblioteca);
                case 3 -> biblioteca.mostrarPrestamos();
                case 4 -> System.out.println("↩ Volviendo al menú principal...");
                default -> System.out.println(" Opción inválida.");
            }
        } while (opcion != 4);
    }

    private static void registrarPrestamo(Scanner sc, Biblioteca biblioteca) {
        biblioteca.mostrarUsuarios();
        System.out.print("Ingrese ID del usuario: ");
        String idUsuario = sc.nextLine();

        biblioteca.mostrarRecursos();
        System.out.print("Ingrese ID del recurso: ");
        int idRecurso = sc.nextInt();
        sc.nextLine();

        Usuario u = biblioteca.consultarUsuarios().stream()
                .filter(us -> us.getIdUsuario().equals(idUsuario))
                .findFirst()
                .orElse(null);

        Recurso r = biblioteca.buscarRecursoPorId(idRecurso);

        if (u == null) {
            System.out.println(ROJO + " Usuario no encontrado." + RESET);
            return;
        }
        if (r == null || !r.isDisponible()) {
            System.out.println(ROJO + "⚠ Recurso no disponible o inexistente." + RESET);
            return;
        }

        biblioteca.registrarPrestamo(u, r);
        System.out.println(VERDE + " Préstamo registrado correctamente." + RESET);
    }

            // DEVOLVER  PRESTAMOS + BASE DE DATOS + PENALIZACION
    private static void devolverPrestamo(Scanner sc, Biblioteca biblioteca) {
        PrestamoDAO prestamoDAO = new PrestamoDAO();

        biblioteca.mostrarPrestamos();
        System.out.print("Ingrese ID del préstamo a devolver: ");
        int id = sc.nextInt();
        sc.nextLine();

        System.out.print("Ingrese días de retraso (0 si no hubo): ");
        int diasRetraso = sc.nextInt();
        sc.nextLine();

        // Llamamos al método del DAO
        prestamoDAO.devolverPrestamo(id, diasRetraso);

        // También reflejamos el cambio en la lógica de objetos en memoria
        Prestamo p = biblioteca.consultarPrestamos().stream()
                .filter(pr -> pr.getIdPrestamo() == id)
                .findFirst()
                .orElse(null);

        if (p != null) {
            p.devolverRecurso();
            System.out.println(VERDE + "✅ Devolución procesada correctamente." + RESET);
        } else {
            System.out.println(ROJO + " No se encontró el préstamo en memoria." + RESET);
        }
    }


    // ===========================================================
    //                      MOSTRAR TODO
    // ===========================================================
    private static void mostrarTodo(Biblioteca biblioteca) throws SQLException {
        biblioteca.mostrarUsuarios();
        biblioteca.mostrarRecursos();
        biblioteca.mostrarPrestamos();
        String url = "jdbc:sqlserver://localhost:1433;databaseName=DB_BIBLIOTECA;encrypt=true;trustServerCertificate=true";
        String user = "sa";
        String password = "12345";

        Connection conn = DriverManager.getConnection(url, user, password);
        System.out.println(" Conexión establecida correctamente");

    }
}

package sgbu;

import java.time.LocalDate;
import java.time.temporal.ChronoUnit;

// NO  es necesario poner un HashMap ni un HashSet en la clase Prestamo ya que con List(dentro de la clase Biblioteca para manejar los préstamos) + base de datos ya se tiene todo lo necesario para registrar, mostrar, devolver y penalizar préstamos.
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

    public int getIdPrestamo() {
        return idPrestamo; }
    public Usuario getUsuario() {
        return usuario; }
    public Recurso getRecurso() {
        return recurso; }
    public LocalDate getFechaInicio() {
        return fechaInicio; }
    public LocalDate getFechaDevolucion() {
        return fechaDevolucion; }
    public String getEstado() {
        return estado; }

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

package sgbu;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import sgbu.DBConnection;
import java.time.LocalDate;
import sgbu.Prestamo;
import sgbu.Usuario;
import sgbu.Recurso;

// PrestamoDAO esta actualizado porque se puso  DEVOLVER PRÉSTAMO Y REGISTRAR PENALIZACIÓN incluyendo actualizacion estado de prestamo y Si hubo retraso, registrar penalización
public class PrestamoDAO {

    // INSERTAR PRÉSTAMO
    public void insertarPrestamo(Prestamo prestamo) {
        String sql = "INSERT INTO Prestamos (idUsuario, idRecurso, fechaInicio, fechaDevolucion, estado) "
                + "VALUES (?, ?, ?, ?, ?)";


        try (Connection conn = DBConnection.getConnection();
             PreparedStatement ps = conn.prepareStatement(sql)) {

            ps.setString(1, prestamo.getUsuario().getIdUsuario());
            ps.setInt(2, prestamo.getRecurso().getId());
            ps.setDate(3, Date.valueOf(prestamo.getFechaInicio()));
            ps.setDate(4, Date.valueOf(prestamo.getFechaDevolucion()));
            ps.setString(5, prestamo.getEstado());

            ps.executeUpdate();
            System.out.println(" Préstamo registrado correctamente en la base de datos.");

        } catch (SQLException e) {
            System.err.println(" Error al insertar préstamo: " + e.getMessage());
        }
    }

    // LISTAR TODOS LOS PRÉSTAMOS
    public List<Prestamo> obtenerPrestamos() {
        List<Prestamo> lista = new ArrayList<>();
        String sql = "SELECT p.idPrestamo, p.idUsuario, a.nombre AS nombreUsuario, " +
                "p.idRecurso, l.titulo AS tituloLibro, l.autor, " +
                "p.fechaInicio, p.fechaDevolucion, p.estado " +
                "FROM Prestamos p " +
                "JOIN Alumnos a ON p.idUsuario = a.idUsuario " +
                "JOIN LibrosFisicos l ON p.idRecurso = l.id";

        try (Connection conn = DBConnection.getConnection();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                // Crear objetos asociados
                Usuario u = new Alumno(rs.getString("idUsuario"), rs.getString("nombreUsuario"),
                        "", "", "", "", "", 0);

                Recurso r = new LibroFisico(rs.getInt("idRecurso"), rs.getString("tituloLibro"),
                        LocalDate.now(), "", "", rs.getString("autor"), 0);

                Prestamo p = new Prestamo(u, r,
                        rs.getDate("fechaInicio").toLocalDate(),
                        rs.getDate("fechaDevolucion").toLocalDate());

                p.setIdPrestamo(rs.getInt("idPrestamo"));
                p.setEstado(rs.getString("estado"));

                lista.add(p);
            }

        } catch (SQLException e) {
            System.err.println(" Error al obtener préstamos: " + e.getMessage());
        }

        return lista;
    }


    // ELIMINAR PRÉSTAMO
    public void eliminarPrestamo(int idPrestamo) {
        String sql = "DELETE FROM Prestamos WHERE idPrestamo = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement ps = conn.prepareStatement(sql)) {

            ps.setInt(1, idPrestamo);
            int filas = ps.executeUpdate();

            if (filas > 0) System.out.println("🗑️ Préstamo eliminado correctamente.");
            else System.out.println("⚠ No se encontró préstamo con ese ID.");

        } catch (SQLException e) {
            System.err.println(" Error al eliminar préstamo: " + e.getMessage());
        }

    }
    // DEVOLVER PRÉSTAMO Y REGISTRAR PENALIZACIÓN
    public void devolverPrestamo(int idPrestamo, int diasRetraso) {
        String sqlUpdate = "UPDATE Prestamos SET estado = 'Devuelto', fechaDevolucion = GETDATE() WHERE idPrestamo = ?";

        try (Connection conn = DBConnection.getConnection();
             PreparedStatement psUpdate = conn.prepareStatement(sqlUpdate)) {

            // Actualiza estado del préstamo
            psUpdate.setInt(1, idPrestamo);
            int filas = psUpdate.executeUpdate();

            if (filas > 0) {
                System.out.println("✅ Préstamo actualizado como devuelto en la base de datos.");

                // Si hubo retraso, registrar penalización
                if (diasRetraso > 0) {
                    double monto = diasRetraso * 1.5; // misma tarifa que en tu clase Penalizacion

                    String sqlPenal = "INSERT INTO Penalizaciones (idPrestamo, diasRetraso, monto) VALUES (?, ?, ?)";
                    try (PreparedStatement psPenal = conn.prepareStatement(sqlPenal)) {
                        psPenal.setInt(1, idPrestamo);
                        psPenal.setInt(2, diasRetraso);
                        psPenal.setDouble(3, monto);
                        psPenal.executeUpdate();
                        System.out.println("💰 Penalización registrada: " + monto + " por " + diasRetraso + " días de retraso.");
                    }
                }

            } else {
                System.out.println("⚠️ No se encontró préstamo con ese ID.");
            }

        } catch (SQLException e) {
            System.err.println("❌ Error al devolver préstamo: " + e.getMessage());

        }

    }

}










































package sgbu;
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



package sgbu;
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


package sgbu;
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

package sgbu;
public interface Reservable {
    boolean reservar(String quien, java.time.LocalDate fechaInicio, java.time.LocalDate fechaFin);
    void liberar();
    boolean estaReservado();
}


package sgbu;
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

package sgbu;
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


package sgbu;
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



package sgbu;
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

package sgbu;

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


package sgbu;
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


package sgbu;
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

package sgbu;
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

package sgbu;

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
  
  
  package sgbu;
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

package sgbu;

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

package sgbu;
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


package sgbu;
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





