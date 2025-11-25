package sgbu;

import javafx.application.Application;
import javafx.stage.Stage;

public class Main extends Application {

    static {
        // ✅ Configurar ANTES de que JavaFX cargue cualquier cosa
        System.setProperty("prism.order", "sw");
        System.setProperty("prism.text", "t2k");
    }

    @Override
    public void start(Stage stage) {
        // ✅ Desactivar el stylesheet por defecto de JavaFX para evitar IllegalAccessError
        Application.setUserAgentStylesheet(null);

        LoginView login = new LoginView();
        login.mostrar(stage);
    }

    public static void main(String[] args) {
        launch(args);
    }
}






package sgbu.MODEL;

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


package sgbu.MODEL;

public interface Reservable {
    boolean reservar(String quien, java.time.LocalDate fechaInicio, java.time.LocalDate fechaFin);
    void liberar();
    boolean estaReservado();
}



package sgbu.MODEL;

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

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }
}



package sgbu.MODEL;

import java.time.LocalDate;
import java.util.Arrays;
import java.util.List;

public class Revista extends RecursoFisico {
    private int volumen;
    private int numero;

    public Revista(int id, String titulo, LocalDate fechaIngreso,
                   String ubicacion, String condicion, int volumen, int numero) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.volumen = volumen;
        this.numero = numero;
    }

    public List<String> consultarArticulos() {
        return Arrays.asList("Artículo 1", "Artículo 2");
    }

    @Override
    public String consultarDetalles() {
        return String.format("Revista[id=%d, titulo=%s, vol=%d, num=%d, disponible=%b]",
                id, titulo, volumen, numero, disponible);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }

    public int getVolumen() {
        return volumen;
    }

    public int getNumero() {
        return numero;
    }

}



package sgbu.MODEL;

import java.time.LocalDate;

public abstract class Dispositivo extends RecursoFisico {
    protected String marca;
    protected String modelo;

    public Dispositivo(int id, String titulo, LocalDate fechaIngreso,
                       String ubicacion, String condicion, String marca, String modelo) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.marca = marca;
        this.modelo = modelo;
    }

    public void encender() {
        System.out.println(titulo + " encendido.");
    }

    public void apagar() {
        System.out.println(titulo + " apagado.");
    }
    public String getMarca() {
        return marca;
    }

    public String getModelo() {
        return modelo;
    }

}


package sgbu.MODEL;


import java.time.LocalDate;

public class Laptop extends Dispositivo {
    private int ramGb;
    private String procesador;

    public Laptop(int id, String titulo, LocalDate fechaIngreso,
                  String ubicacion, String condicion, String marca, String modelo,
                  int ramGb, String procesador) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, marca, modelo);
        this.ramGb = ramGb;
        this.procesador = procesador;
    }

    public void instalarSoftware(String nombre) {
        System.out.println("Instalando " + nombre + " en " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Laptop[id=%d, titulo=%s, marca=%s, RAM=%dGB, proc=%s]",
                id, titulo, marca, ramGb, procesador);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }

    public int getRamGb() {
        return ramGb;
    }

    public String getProcesador() {
        return procesador;
    }




package sgbu.MODEL;

import java.time.LocalDate;

public class Tablet extends Dispositivo {
    private double tamanoPantalla;

    public Tablet(int id, String titulo, LocalDate fechaIngreso,
                  String ubicacion, String condicion, String marca, String modelo,
                  double tamanoPantalla) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, marca, modelo);
        this.tamanoPantalla = tamanoPantalla;
    }

    public void abrirApp(String nombre) {
        System.out.println("Abriendo " + nombre + " en " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Tablet[id=%d, titulo=%s, marca=%s, pantalla=%.1f in]", id, titulo, marca, tamanoPantalla);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }

    public String getMarca() {
        return marca;
    }

    public String getModelo() {
        return modelo;
    }

    public double getTamanoPantalla() {
        return tamanoPantalla;
    }

    @Override
    public String getUbicacionEstante() {
        return ubicacionEstante;
    }
}





package sgbu.MODEL;

import java.time.LocalDate;

public abstract class Instrumento extends RecursoFisico {
    protected String descripcion;

    public Instrumento(int id, String titulo, LocalDate fechaIngreso,
                       String ubicacion, String condicion, String descripcion) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.descripcion = descripcion;
    }
    // En Instrumento
    public String getDescripcion() {
        return descripcion;
    }
}



package sgbu.MODEL;

import java.time.LocalDate;

public class Multimetro extends Instrumento {
    private String rango;

    public Multimetro(int id, String titulo, LocalDate fechaIngreso,
                      String ubicacion, String condicion, String descripcion, String rango) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, descripcion);
        this.rango = rango;
    }

    public boolean autoverificar() {
        System.out.println("Autoverificando " + titulo);
        return true;
    }

    @Override
    public String consultarDetalles() {
        return String.format("Multimetro[id=%d, titulo=%s, rango=%s]", id, titulo, rango);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }

    // En Multimetro
    public String getRango() {
        return rango;
    }
}




package sgbu.MODEL;

import java.time.LocalDate;

public class Microscopio extends Instrumento {
    private int aumentosMax;

    public Microscopio(int id, String titulo, LocalDate fechaIngreso,
                       String ubicacion, String condicion, String descripcion, int aumentosMax) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, descripcion);
        this.aumentosMax = aumentosMax;
    }

    public void calibrar() {
        System.out.println("Calibrando " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Microscopio[id=%d, titulo=%s, aumentos=%d]", id, titulo, aumentosMax);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }

    // En Microscopio
    public int getAumentosMax() {
        return aumentosMax;
    }
}




package sgbu.MODEL;

import java.time.LocalDate;

public class Proyector extends Instrumento {
    private String resolucion;
    private int brillo;

    public Proyector(int id, String titulo, LocalDate fechaIngreso,
                     String ubicacion, String condicion, String descripcion,
                     String resolucion, int brillo) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, descripcion);
        this.resolucion = resolucion;
        this.brillo = brillo;
    }

    public void proyectarArchivo(String nombreArchivo) {
        System.out.println("Proyectando " + nombreArchivo + " en " + titulo);
    }

    @Override
    public String consultarDetalles() {
        return String.format("Proyector[id=%d, titulo=%s, resol=%s, brillo=%d]", id, titulo, resolucion, brillo);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }

    // En Proyector
    public String getResolucion() {
        return resolucion;
    }
    public int getBrillo() {
        return brillo;
    }
}




package sgbu.MODEL;

import java.time.LocalDate;

public abstract class Ambiente extends RecursoFisico {
    protected int capacidad;
    public Ambiente(int id, String titulo, LocalDate fechaIngreso, String ubicacion, String condicion, int capacidad) {
        super(id, titulo, fechaIngreso, ubicacion, condicion);
        this.capacidad = capacidad;
    }
    public int getCapacidad() {
        return capacidad; }

    @Override
    public String consultarDetalles() {
        return String.format("Ambiente[id=%d,titulo=%s,capacidad=%d]", id, titulo, capacidad);
    }
}



package sgbu.MODEL;

import java.time.LocalDate;

public class SalaEstudio extends Ambiente {
    private int numMesas;

    public SalaEstudio(int id, String titulo, LocalDate fechaIngreso,
                       String ubicacion, String condicion, int capacidad, int numMesas) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, capacidad);
        this.numMesas = numMesas;
    }

    public boolean consultarDisponibilidadMesa() {
        return disponible;
    }

    @Override
    public String consultarDetalles() {
        return String.format("SalaEstudio[id=%d, titulo=%s, mesas=%d]", id, titulo, numMesas);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }

    // SalaEstudio
    public int getNumMesas() {
        return numMesas;
    }
    
}




package sgbu.MODEL;

import java.time.LocalDate;

public class SalaLectura extends Ambiente {
    private int numSillas;

    public SalaLectura(int id, String titulo, LocalDate fechaIngreso,
                       String ubicacion, String condicion, int capacidad, int numSillas) {
        super(id, titulo, fechaIngreso, ubicacion, condicion, capacidad);
        this.numSillas = numSillas;
    }

    public int consultarCapacidad() {
        return capacidad;
    }

    @Override
    public String consultarDetalles() {
        return String.format("SalaLectura[id=%d, titulo=%s, sillas=%d]", id, titulo, numSillas);
    }

    @Override
    public void setCantidadDisponible(int cantidadDisponible) {

    }

    @Override
    public int getCantidadDisponible() {
        return 0;
    }

    @Override
    public String getAutor() {
        return "Autor";
    }
    // SalaEstudio


    // SalaLectura
    public int getNumSillas() {
        return numSillas;
    }

}






// CONTROLES 



package sgbu.CONTROLLER;

import javafx.fxml.FXMLLoader;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;
import sgbu.DAO.*;
import sgbu.MODEL.Biblioteca;
import sgbu.MODEL.SistemaBiblioteca;

public class BibliotecarioViewController {

    private final UsuarioDAO usuarioDAO = new UsuarioDAO();
    private final RecursoDAO recursoDAO = new RecursoDAO();
    private final PrestamoDAO prestamoDAO = new PrestamoDAO();
    private final DevolucionDAO devolucionDAO = new DevolucionDAO();
    private final Biblioteca biblioteca = SistemaBiblioteca.biblioteca;

    public void mostrar(Stage stage) {
        VBox root = new VBox(18);
        root.setAlignment(Pos.CENTER);
        root.setPadding(new Insets(40));
        root.setStyle("-fx-background-color: #f4f4f4;");

        Label titulo = new Label("📚 Panel del Bibliotecario");
        titulo.setStyle("-fx-font-size: 25px;");

        Button btnUsuarios = new Button("👥 Gestión de Usuarios");
        btnUsuarios.setPrefWidth(250);
        btnUsuarios.setOnAction(e -> abrirMenuUsuarios());

        Button btnRecursos = new Button("📚 Gestión de Recursos");
        btnRecursos.setPrefWidth(250);
        btnRecursos.setOnAction(e -> abrirMenuRecursos());

        Button btnPrestamos = new Button("📋 Préstamos y Devoluciones");
        btnPrestamos.setPrefWidth(250);
        btnPrestamos.setOnAction(e -> abrirMenuPrestamos());

        Button btnConsultas = new Button("📊 Consultas / Reportes");
        btnConsultas.setPrefWidth(250);
        btnConsultas.setOnAction(e -> abrirMenuConsultas());

        Button btnCerrarSesion = new Button("🔒 Cerrar Sesión");
        btnCerrarSesion.setPrefWidth(250);
        btnCerrarSesion.setStyle("-fx-background-color: #d9534f; -fx-text-fill: white;");
        btnCerrarSesion.setOnAction(e -> stage.close());

        root.getChildren().addAll(
                titulo, btnUsuarios, btnRecursos,
                btnPrestamos, btnConsultas, btnCerrarSesion
        );

        Scene scene = new Scene(root, 900, 600);
        stage.setScene(scene);
        stage.setTitle("Panel del Bibliotecario");
        stage.show();
    }

    private void abrirMenuUsuarios() {
        abrirVentana("/sgbu/VIEW/GestionUsuariosView.fxml", "Gestión de Usuarios");
    }

    private void abrirMenuRecursos() {
        abrirVentana("/sgbu/VIEW/GestionRecursosView.fxml", "Gestión de Recursos");
    }

    private void abrirMenuPrestamos() {
        abrirVentana("/sgbu/VIEW/GestionPrestamosView.fxml", "Préstamos y Devoluciones");
    }

    private void abrirMenuConsultas() {
        biblioteca.mostrarPrestamosBD();
    }

    private void abrirVentana(String rutaFxml, String titulo) {
        try {
            System.out.println("=== DEBUG CARGA FXML ===");
            System.out.println("Intentando cargar: " + rutaFxml);

            // Verificar si el recurso existe
            java.net.URL recursoURL = getClass().getResource(rutaFxml);
            System.out.println("URL del recurso: " + recursoURL);

            if (recursoURL == null) {
                // Intentar rutas alternativas
                System.out.println("Intentando sin /sgbu...");
                recursoURL = getClass().getResource(rutaFxml.replace("/sgbu/", "/"));
                System.out.println("URL alternativa: " + recursoURL);
            }

            if (recursoURL == null) {
                throw new Exception("❌ Archivo FXML no encontrado en: " + rutaFxml);
            }

            FXMLLoader loader = new FXMLLoader();
            loader.setLocation(recursoURL);
            Parent root = loader.load();

            Stage stage = new Stage();
            stage.setScene(new Scene(root, 800, 600));
            stage.setTitle(titulo);
            stage.show();

            System.out.println("✅ Ventana abierta exitosamente");

        } catch (Exception e) {
            System.err.println("❌ ERROR AL CARGAR FXML:");
            e.printStackTrace();

            Alert alert = new Alert(Alert.AlertType.ERROR);
            alert.setTitle("Error");
            alert.setHeaderText("No se pudo abrir la ventana");
            alert.setContentText("Error al cargar: " + rutaFxml + "\n\n" +
                    "Causa: " + e.getMessage() + "\n\n" +
                    "Verifica que el archivo existe en src/sgbu/VIEW/");
            alert.showAndWait();
        }
    }
}



package sgbu.CONTROLLER;

import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.fxml.FXML;
import javafx.scene.control.*;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.stage.Stage;
import sgbu.DAO.PrestamoDAO;
import sgbu.MODEL.Prestamo;
import java.time.LocalDate;

public class GestionPrestamosController {

    // Búsqueda y filtros
    @FXML private TextField txtBuscarPrestamo;
    @FXML private TextField txtBuscarHistorial;
    @FXML private ComboBox<String> cmbEstadoPrestamo;
    @FXML private DatePicker dpFechaInicio;
    @FXML private DatePicker dpFechaFin;

    // Botones principales
    @FXML private Button btnBuscarPrestamo;
    @FXML private Button btnBuscarHistorial;
    @FXML private Button btnNuevoPrestamo;
    @FXML private Button btnRegistrarDevolucion;
    @FXML private Button btnRenovar;
    @FXML private Button btnVolver;

    // Tabla de préstamos activos
    @FXML private TableView<Prestamo> tablaPrestamos;
    @FXML private TableColumn<Prestamo, Integer> colPrestamoId;
    @FXML private TableColumn<Prestamo, String> colUsuario;
    @FXML private TableColumn<Prestamo, String> colRecurso;
    @FXML private TableColumn<Prestamo, LocalDate> colFechaInicio;
    @FXML private TableColumn<Prestamo, LocalDate> colFechaDevolucion;
    @FXML private TableColumn<Prestamo, String> colEstadoPrestamo;
    @FXML private TableColumn<Prestamo, Void> colAccionesPrestamo;

    // Tabla de historial
    @FXML private TableView<Prestamo> tablaHistorial;
    @FXML private TableColumn<Prestamo, Integer> colHistorialId;
    @FXML private TableColumn<Prestamo, String> colHistorialUsuario;
    @FXML private TableColumn<Prestamo, String> colHistorialRecurso;
    @FXML private TableColumn<Prestamo, LocalDate> colHistorialPrestamo;
    @FXML private TableColumn<Prestamo, LocalDate> colHistorialDevolucion;
    @FXML private TableColumn<Prestamo, String> colHistorialEstado;

    // Labels de estadísticas
    @FXML private Label lblTotalPrestamos;
    @FXML private Label lblActivos;
    @FXML private Label lblDevueltos;
    @FXML private Label lblVencidos;

    private final PrestamoDAO prestamoDAO = new PrestamoDAO();
    private ObservableList<Prestamo> listaPrestamos = FXCollections.observableArrayList(prestamoDAO.obtenerPrestamos());
    private ObservableList<Prestamo> listaHistorial;

    @FXML
    public void initialize() {
        configurarComboBoxes();
        configurarTablas();
        cargarDatos();
        configurarEventos();
        actualizarEstadisticas();
    }

    private void configurarComboBoxes() {
        cmbEstadoPrestamo.setItems(FXCollections.observableArrayList(
                "Todos", "Activo", "Vencido", "Devuelto"
        ));
        cmbEstadoPrestamo.setValue("Activo");
    }

    private void configurarTablas() {
        // Configurar tabla de préstamos activos
        colPrestamoId.setCellValueFactory(new PropertyValueFactory<>("id"));
        colUsuario.setCellValueFactory(cellData ->
                new javafx.beans.property.SimpleStringProperty(
                        cellData.getValue().getUsuario().getNombre()
                ));
        colRecurso.setCellValueFactory(cellData ->
                new javafx.beans.property.SimpleStringProperty(
                        cellData.getValue().getRecurso().getTitulo()
                ));
        colFechaInicio.setCellValueFactory(new PropertyValueFactory<>("fechaInicio"));
        colFechaDevolucion.setCellValueFactory(new PropertyValueFactory<>("fechaDevolucion"));
        colEstadoPrestamo.setCellValueFactory(new PropertyValueFactory<>("estado"));

        // Configurar tabla de historial
        colHistorialId.setCellValueFactory(new PropertyValueFactory<>("id"));
        colHistorialUsuario.setCellValueFactory(cellData ->
                new javafx.beans.property.SimpleStringProperty(
                        cellData.getValue().getUsuario().getNombre()
                ));
        colHistorialRecurso.setCellValueFactory(cellData ->
                new javafx.beans.property.SimpleStringProperty(
                        cellData.getValue().getRecurso().getTitulo()
                ));
        colHistorialPrestamo.setCellValueFactory(new PropertyValueFactory<>("fechaInicio"));
        colHistorialDevolucion.setCellValueFactory(new PropertyValueFactory<>("fechaDevolucion"));
        colHistorialEstado.setCellValueFactory(new PropertyValueFactory<>("estado"));
    }

    private void cargarDatos() {

        // Separar activos de historial
        ObservableList<Prestamo> activos = FXCollections.observableArrayList();
        ObservableList<Prestamo> historial = FXCollections.observableArrayList();

        for (Prestamo p : listaPrestamos) {
            if (p.getEstado().equalsIgnoreCase("Activo") ||
                    p.getEstado().equalsIgnoreCase("Vencido")) {
                activos.add(p);
            } else {
                historial.add(p);
            }
        }

        tablaPrestamos.setItems(activos);
        listaHistorial = historial;
        tablaHistorial.setItems(listaHistorial);
    }

    private void configurarEventos() {
        btnBuscarPrestamo.setOnAction(e -> buscarPrestamos());
        btnBuscarHistorial.setOnAction(e -> buscarHistorial());
        btnNuevoPrestamo.setOnAction(e -> nuevoPrestamo());
        btnRegistrarDevolucion.setOnAction(e -> registrarDevolucion());
        btnRenovar.setOnAction(e -> renovarPrestamo());
        btnVolver.setOnAction(e -> volver());

        cmbEstadoPrestamo.setOnAction(e -> buscarPrestamos());
    }

    private void buscarPrestamos() {
        String criterio = txtBuscarPrestamo.getText().trim().toLowerCase();
        String estado = cmbEstadoPrestamo.getValue();

        ObservableList<Prestamo> filtrados = FXCollections.observableArrayList();

        for (Prestamo p : listaPrestamos) {
            if (!p.getEstado().equalsIgnoreCase("Devuelto")) {
                boolean cumpleCriterio = criterio.isEmpty() ||
                        p.getUsuario().getNombre().toLowerCase().contains(criterio) ||
                        p.getRecurso().getTitulo().toLowerCase().contains(criterio);

                boolean cumpleEstado = estado.equals("Todos") ||
                        p.getEstado().equalsIgnoreCase(estado);

                if (cumpleCriterio && cumpleEstado) {
                    filtrados.add(p);
                }
            }
        }

        tablaPrestamos.setItems(filtrados);
    }

    private void buscarHistorial() {
        String criterio = txtBuscarHistorial.getText().trim().toLowerCase();
        LocalDate inicio = dpFechaInicio.getValue();
        LocalDate fin = dpFechaFin.getValue();

        ObservableList<Prestamo> filtrados = FXCollections.observableArrayList();

        for (Prestamo p : listaHistorial) {
            boolean cumpleCriterio = criterio.isEmpty() ||
                    p.getUsuario().getNombre().toLowerCase().contains(criterio) ||
                    p.getRecurso().getTitulo().toLowerCase().contains(criterio);

            boolean cumpleFecha = true;
            if (inicio != null && p.getFechaInicio().isBefore(inicio)) {
                cumpleFecha = false;
            }
            if (fin != null && p.getFechaInicio().isAfter(fin)) {
                cumpleFecha = false;
            }

            if (cumpleCriterio && cumpleFecha) {
                filtrados.add(p);
            }
        }

        tablaHistorial.setItems(filtrados);
    }

    private void nuevoPrestamo() {
        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("Nuevo Préstamo");
        alert.setHeaderText("Registrar nuevo préstamo");
        alert.setContentText("Funcionalidad en desarrollo");
        alert.showAndWait();
    }

    private void registrarDevolucion() {
        Prestamo seleccionado = tablaPrestamos.getSelectionModel().getSelectedItem();
        if (seleccionado == null) {
            mostrarAlerta("Selecciona un préstamo para registrar devolución");
            return;
        }

        Alert confirmacion = new Alert(Alert.AlertType.CONFIRMATION);
        confirmacion.setTitle("Registrar Devolución");
        confirmacion.setHeaderText("¿Registrar devolución?");
        confirmacion.setContentText("Recurso: " + seleccionado.getRecurso().getTitulo() +
                "\nUsuario: " + seleccionado.getUsuario().getNombre());

        if (confirmacion.showAndWait().get() == ButtonType.OK) {
            seleccionado.setEstado("Devuelto");
            cargarDatos();
            actualizarEstadisticas();
            mostrarAlerta("Devolución registrada correctamente");
        }
    }

    private void renovarPrestamo() {
        Prestamo seleccionado = tablaPrestamos.getSelectionModel().getSelectedItem();
        if (seleccionado == null) {
            mostrarAlerta("Selecciona un préstamo para renovar");
            return;
        }

        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("Renovar Préstamo");
        alert.setHeaderText("Renovando préstamo");
        alert.setContentText("Funcionalidad en desarrollo");
        alert.showAndWait();
    }

    private void actualizarEstadisticas() {
        int total = listaPrestamos.size();
        int activos = (int) listaPrestamos.stream()
                .filter(p -> p.getEstado().equalsIgnoreCase("Activo")).count();
        int devueltos = (int) listaPrestamos.stream()
                .filter(p -> p.getEstado().equalsIgnoreCase("Devuelto")).count();
        int vencidos = (int) listaPrestamos.stream()
                .filter(p -> p.getEstado().equalsIgnoreCase("Vencido")).count();

        lblTotalPrestamos.setText("Total préstamos: " + total);
        lblActivos.setText("Activos: " + activos);
        lblDevueltos.setText("Devueltos: " + devueltos);
        lblVencidos.setText("Vencidos: " + vencidos);
    }

    private void volver() {
        Stage stage = (Stage) btnVolver.getScene().getWindow();
        stage.close();
    }

    private void mostrarAlerta(String mensaje) {
        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setContentText(mensaje);
        alert.show();
    }
}
  



package sgbu.CONTROLLER;

import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.fxml.FXML;
import javafx.scene.control.*;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.stage.Stage;
import sgbu.DAO.RecursoDAO;
import sgbu.MODEL.Recurso;

public class GestionRecursosController {

    @FXML private TextField txtBuscar;
    @FXML private ComboBox<String> cmbTipo;
    @FXML private ComboBox<String> cmbEstado;
    @FXML private Button btnBuscar;
    @FXML private Button btnAgregar;
    @FXML private Button btnEditar;
    @FXML private Button btnEliminar;
    @FXML private Button btnVolver;

    @FXML private TableView<Recurso> tablaRecursos;
    @FXML private TableColumn<Recurso, String> colId;
    @FXML private TableColumn<Recurso, String> colTitulo;
    @FXML private TableColumn<Recurso, String> colAutor;
    @FXML private TableColumn<Recurso, String> colTipo;
    @FXML private TableColumn<Recurso, String> colEstado;
    @FXML private TableColumn<Recurso, Integer> colDisponibles;
    @FXML private TableColumn<Recurso, Void> colAcciones;

    @FXML private Label lblTotalRecursos;
    @FXML private Label lblDisponibles;
    @FXML private Label lblPrestados;

    private final RecursoDAO recursoDAO = new RecursoDAO();
    private ObservableList<Recurso> listaRecursos;

    public GestionRecursosController() {
        listaRecursos = FXCollections.observableArrayList(recursoDAO.obtenerExtrasDeRecurso());
    }

    @FXML
    public void initialize() {
        configurarComboBoxes();
        configurarTabla();
        cargarRecursos();
        configurarEventos();
        actualizarEstadisticas();
    }

    private void configurarComboBoxes() {
        cmbTipo.setItems(FXCollections.observableArrayList(
                "Todos", "Libro", "Revista", "Digital"
        ));
        cmbTipo.setValue("Todos");

        cmbEstado.setItems(FXCollections.observableArrayList(
                "Todos", "Disponible", "Prestado", "Mantenimiento"
        ));
        cmbEstado.setValue("Todos");
    }

    private void configurarTabla() {
        colId.setCellValueFactory(new PropertyValueFactory<>("id"));
        colTitulo.setCellValueFactory(new PropertyValueFactory<>("titulo"));
        colAutor.setCellValueFactory(new PropertyValueFactory<>("autor"));
        colTipo.setCellValueFactory(cellData -> {
            String tipo = cellData.getValue().getClass().getSimpleName();
            return new javafx.beans.property.SimpleStringProperty(tipo);
        });
        colEstado.setCellValueFactory(cellData -> {
            boolean disponible = cellData.getValue().isDisponible();
            String estado = disponible ? "Disponible" : "Prestado";
            return new javafx.beans.property.SimpleStringProperty(estado);
        });
        colDisponibles.setCellValueFactory(new PropertyValueFactory<>("cantidadDisponible"));
    }

    private void cargarRecursos() {
        tablaRecursos.setItems(listaRecursos);
    }

    private void configurarEventos() {
        btnBuscar.setOnAction(e -> buscarRecursos());
        btnAgregar.setOnAction(e -> agregarRecurso());
        btnEditar.setOnAction(e -> editarRecurso());
        btnEliminar.setOnAction(e -> eliminarRecurso());
        btnVolver.setOnAction(e -> volver());

        cmbTipo.setOnAction(e -> buscarRecursos());
        cmbEstado.setOnAction(e -> buscarRecursos());
    }

    private void buscarRecursos() {
        String criterio = txtBuscar.getText().trim().toLowerCase();
        String tipo = cmbTipo.getValue();
        String estado = cmbEstado.getValue();

        ObservableList<Recurso> filtrados = FXCollections.observableArrayList();

        for (Recurso r : listaRecursos) {
            boolean cumpleCriterio = criterio.isEmpty() ||
                    r.getTitulo().toLowerCase().contains(criterio) ||
                    r.getAutor().toLowerCase().contains(criterio);

            boolean cumpleTipo = tipo.equals("Todos") ||
                    r.getClass().getSimpleName().equalsIgnoreCase(tipo);

            boolean cumpleEstado = estado.equals("Todos") ||
                    (estado.equals("Disponible") && r.isDisponible()) ||
                    (estado.equals("Prestado") && !r.isDisponible());

            if (cumpleCriterio && cumpleTipo && cumpleEstado) {
                filtrados.add(r);
            }
        }

        tablaRecursos.setItems(filtrados);
        actualizarEstadisticas();
    }

    private void agregarRecurso() {
        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("Agregar Recurso");
        alert.setHeaderText("Funcionalidad en desarrollo");
        alert.setContentText("Aquí podrás agregar un nuevo recurso al catálogo.");
        alert.showAndWait();
    }

    private void editarRecurso() {
        Recurso seleccionado = tablaRecursos.getSelectionModel().getSelectedItem();
        if (seleccionado == null) {
            mostrarAlerta("Selecciona un recurso para editar");
            return;
        }

        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("Editar Recurso");
        alert.setHeaderText("Editando: " + seleccionado.getTitulo());
        alert.setContentText("Funcionalidad en desarrollo");
        alert.showAndWait();
    }

    private void eliminarRecurso() {
        Recurso seleccionado = tablaRecursos.getSelectionModel().getSelectedItem();
        if (seleccionado == null) {
            mostrarAlerta("Selecciona un recurso para eliminar");
            return;
        }

        Alert confirmacion = new Alert(Alert.AlertType.CONFIRMATION);
        confirmacion.setTitle("Confirmar eliminación");
        confirmacion.setHeaderText("¿Eliminar recurso?");
        confirmacion.setContentText("Se eliminará: " + seleccionado.getTitulo());

        if (confirmacion.showAndWait().get() == ButtonType.OK) {
            // recursoDAO.eliminar(seleccionado.getId());
            listaRecursos.remove(seleccionado);
            actualizarEstadisticas();
            mostrarAlerta("Recurso eliminado correctamente");
        }
    }

    private void actualizarEstadisticas() {
        int total = tablaRecursos.getItems().size();
        int disponibles = (int) tablaRecursos.getItems().stream()
                .filter(Recurso::isDisponible).count();
        int prestados = total - disponibles;

        lblTotalRecursos.setText("Total: " + total);
        lblDisponibles.setText("Disponibles: " + disponibles);
        lblPrestados.setText("Prestados: " + prestados);
    }

    private void volver() {
        Stage stage = (Stage) btnVolver.getScene().getWindow();
        stage.close();
    }

    private void mostrarAlerta(String mensaje) {
        Alert alert = new Alert(Alert.AlertType.WARNING);
        alert.setContentText(mensaje);
        alert.show();
    }
} 




package sgbu.CONTROLLER;

import javafx.fxml.FXML;
import javafx.scene.control.*;
import javafx.scene.input.MouseEvent;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import sgbu.DAO.UsuarioDAO;
import sgbu.MODEL.*;

public class GestionUsuariosController {

    @FXML
    private TextField txtId;
    @FXML
    private TextField txtNombre;
    @FXML
    private TextField txtCorreo;
    @FXML
    private TextField txtTelefono;
    @FXML
    private PasswordField txtPassword;

    @FXML
    private ComboBox<String> cbTipoUsuario;

    // Campos Alumno
    @FXML
    private TextField txtCarrera;
    @FXML
    private TextField txtCodigoAlumno;
    @FXML
    private TextField txtCiclo;

    // Campos Profesor
    @FXML
    private TextField txtFacultad;
    @FXML
    private TextField txtEspecialidad;
    @FXML
    private TextField txtCodigoProfesor;

    // Campos Bibliotecario
    @FXML
    private TextField txtIdEmpleado;
    @FXML
    private TextField txtTurno;
    @FXML
    private TextField txtEspecialidadBib;

    // Campos Invitado
    @FXML
    private TextField txtTipoInvitado;
    @FXML
    private DatePicker dpFechaExpiracion;

    @FXML
    private TextField txtMinutosPermitidos;

    private UsuarioDAO usuarioDAO = new UsuarioDAO();

    @FXML
    public void initialize() {

        ObservableList<String> tipos = FXCollections.observableArrayList(
                "ALUMNO", "PROFESOR", "BIBLIOTECARIO", "INVITADO"
        );
        cbTipoUsuario.setItems(tipos);

        ocultarTodosLosCampos();
    }

    private void ocultarTodosLosCampos() {
        txtCarrera.setVisible(false);
        txtCodigoAlumno.setVisible(false);
        txtCiclo.setVisible(false);

        txtFacultad.setVisible(false);
        txtEspecialidad.setVisible(false);
        txtCodigoProfesor.setVisible(false);

        txtIdEmpleado.setVisible(false);
        txtTurno.setVisible(false);
        txtEspecialidadBib.setVisible(false);

        txtTipoInvitado.setVisible(false);
        dpFechaExpiracion.setVisible(false);
    }

    @FXML
    private void cambiarTipoUsuario() {

        ocultarTodosLosCampos();

        String tipo = cbTipoUsuario.getValue();

        if (tipo == null)
            return;

        switch (tipo) {

            case "ALUMNO" -> {
                txtCarrera.setVisible(true);
                txtCodigoAlumno.setVisible(true);
                txtCiclo.setVisible(true);
            }

            case "PROFESOR" -> {
                txtFacultad.setVisible(true);
                txtEspecialidad.setVisible(true);
                txtCodigoProfesor.setVisible(true);
            }

            case "BIBLIOTECARIO" -> {
                txtIdEmpleado.setVisible(true);
                txtTurno.setVisible(true);
                txtEspecialidadBib.setVisible(true);
            }

            case "INVITADO" -> {
                txtTipoInvitado.setVisible(true);
                dpFechaExpiracion.setVisible(true);
            }
        }
    }

    // =====================================================
    //  BOTÓN REGISTRAR
    // =====================================================
    @FXML
    private void registrarUsuario(MouseEvent event) {

    }

    private void mostrarMensaje(String s) {
    }
}




// Views



package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.TextInputDialog;
import javafx.scene.control.Label;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;
import javafx.animation.TranslateTransition;
import javafx.util.Duration;

import java.util.Optional;

public class AmbientesView {

    public void mostrar(Stage stage, String rol) {

        BorderPane root = new BorderPane();
        root.setPadding(new Insets(20));

        // ░░░ BARRA SUPERIOR (ROL + AVATAR) ░░░
        HBox topBar = new HBox();
        topBar.setPadding(new Insets(10, 25, 10, 25));
        topBar.setAlignment(Pos.CENTER_RIGHT);
        topBar.setSpacing(12);

        Label rolLabel = new Label("Rol: " + rol);
        rolLabel.setStyle("-fx-font-size: 14px; -fx-font-weight: bold;");

        // ░░░ AVATAR LABEL ░░░
        Label avatarLabel = new Label("👤");
        avatarLabel.setStyle("-fx-font-size: 26px;");

        // Animación rebote para avatar Label
        TranslateTransition bounceLabel = new TranslateTransition(Duration.millis(220), avatarLabel);
        bounceLabel.setFromY(0);
        bounceLabel.setToY(-6);

        avatarLabel.setOnMouseEntered(e -> { bounceLabel.setRate(1); bounceLabel.playFromStart(); });
        avatarLabel.setOnMouseExited(e -> { bounceLabel.setRate(-1); bounceLabel.play(); });

        // ░░░ AVATAR IMAGEN (el que ya tenías) ░░░
        ImageView avatar = new ImageView();

        try {
            Image img = new Image(getClass().getResourceAsStream("/img/avatar.png"));
            avatar.setImage(img);
        } catch (Exception ex) {
            System.out.println("⚠ No se pudo cargar avatar desde /img/avatar.png");
        }

        avatar.setFitWidth(35);
        avatar.setFitHeight(35);
        avatar.setPreserveRatio(true);

        // animación rebote para avatar imagen
        TranslateTransition bounce = new TranslateTransition(Duration.millis(220), avatar);
        bounce.setFromY(0);
        bounce.setToY(-6);

        avatar.setOnMouseEntered(e -> { bounce.setRate(1); bounce.playFromStart(); });
        avatar.setOnMouseExited(e -> { bounce.setRate(-1); bounce.play(); });

        // AÑADIDO: ahora contiene rol + avatarLabel + avatarImagen
        topBar.getChildren().addAll(rolLabel, avatarLabel, avatar);
        root.setTop(topBar);

        // ░░░ TÍTULO ░░░
        Label titulo = new Label("🪑 Ambientes y Salas");
        titulo.setStyle("-fx-font-size: 22px; -fx-font-weight: bold;");

        Button btnSalaEstudio = new Button("Sala de Estudio (3 - 10 sillas)");
        Button btnSalaLectura = new Button("Sala de Lectura (3 - 5 sillas)");

        btnSalaEstudio.setStyle("-fx-background-color: white; -fx-padding: 10 25; -fx-font-size: 14px; -fx-background-radius: 10;");
        btnSalaLectura.setStyle("-fx-background-color: white; -fx-padding: 10 25; -fx-font-size: 14px; -fx-background-radius: 10;");

        addBounce(btnSalaEstudio);
        addBounce(btnSalaLectura);

        btnSalaEstudio.setOnAction(e -> reservarSala("Sala de Estudio", 3, 10));
        btnSalaLectura.setOnAction(e -> reservarSala("Sala de Lectura", 3, 5));

        VBox centro = new VBox(15);
        centro.setAlignment(Pos.CENTER);
        centro.getChildren().addAll(titulo, btnSalaEstudio, btnSalaLectura);

        root.setCenter(centro);

        Scene scene = new Scene(root, 900, 600);
        scene.setFill(javafx.scene.paint.Color.web("#ECF2FF"));

        stage.setScene(scene);
        stage.setTitle("Ambientes y Salas");
        stage.show();
    }

    private void addBounce(Button btn) {
        TranslateTransition t = new TranslateTransition(Duration.millis(120), btn);
        t.setFromY(0);
        t.setToY(-4);

        btn.setOnMouseEntered(e -> { t.setRate(1); t.playFromStart(); });
        btn.setOnMouseExited(e -> { t.setRate(-1); t.play(); });
    }

    private void reservarSala(String nombreSala, int minSillas, int maxSillas) {
        TextInputDialog dialog = new TextInputDialog();
        dialog.setTitle("Reservar " + nombreSala);
        dialog.setHeaderText("Número de sillas (" + minSillas + " - " + maxSillas + ")");
        dialog.setContentText("Ingrese cuántas sillas desea reservar:");

        Optional<String> result = dialog.showAndWait();
        if (result.isPresent()) {
            try {
                int n = Integer.parseInt(result.get().trim());

                if (n < minSillas || n > maxSillas) {
                    Alert a = new Alert(Alert.AlertType.ERROR);
                    a.setHeaderText("Número inválido");
                    a.setContentText("Debe ingresar entre " + minSillas + " y " + maxSillas + " sillas.");
                    a.showAndWait();
                } else {
                    Alert a = new Alert(Alert.AlertType.INFORMATION);
                    a.setHeaderText("Reserva exitosa");
                    a.setContentText("Se reservó " + nombreSala + " con " + n + " sillas.");
                    a.showAndWait();
                }

            } catch (NumberFormatException ex) {
                Alert a = new Alert(Alert.AlertType.ERROR);
                a.setHeaderText("Entrada inválida");
                a.setContentText("Ingrese un número válido.");
                a.showAndWait();
            }
        }
    }
}



package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.ScrollPane;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class ArticulosDigitalesView {

    public void mostrar(Stage stage, String rol) {

        VBox root = new VBox(25);
        root.setPadding(new Insets(30));
        root.setAlignment(Pos.TOP_CENTER);

        Label titulo = new Label("Artículos Digitales");
        titulo.setStyle("-fx-font-size: 28px; -fx-font-weight: bold; -fx-text-fill:#1E3A8A;");

        root.getChildren().add(titulo);

        root.getChildren().add(crearArticuloCard(
                "Optimización de Bases de Datos SQL", "George Herrera", "2021",
                "Artículo académico sobre técnicas modernas de optimización de consultas.",
                "/img/articulo1.png", rol));

        root.getChildren().add(crearArticuloCard(
                "Inteligencia Artificial en la Educación", "Luis Cabanillas", "2023",
                "Análisis de cómo la IA transforma modelos educativos.",
                "/img/articulo2.png", rol));

        root.getChildren().add(crearArticuloCard(
                "Análisis Cienciométrico (2018–2023)", "Ana Isabel Bonilla", "2024",
                "Análisis de producción científica en acceso abierto.",
                "/img/articulo3.png", rol));

        root.getChildren().add(crearArticuloCard(
                "Internacionalización de Revista REDTIS", "Mónica Olivarría", "2022",
                "Proceso de indexamiento en bases electrónicas.",
                "/img/articulo4.png", rol));

        root.getChildren().add(crearArticuloCard(
                "Herramientas para Gamificar Matemáticas", "Gutiérrez & Peraza", "2023",
                "Estudio sobre gamificación educativa.",
                "/img/articulo5.png", rol));

        root.getChildren().add(crearArticuloCard(
                "Avances en Sistemas y Tecnologías de Información", "Álvaro Rocha", "2025",
                "Publicación de la revista RISTI.",
                "/img/articulo6.png", rol));

        ScrollPane scroll = new ScrollPane(root);
        scroll.setFitToWidth(true);

        Scene scene = new Scene(scroll, 1100, 700);
        stage.setScene(scene);
        stage.show();
    }

    private VBox crearArticuloCard(String titulo, String autor, String anio,
                                   String descripcion, String rutaImagen, String rol) {

        HBox card = new HBox(20);
        card.setPadding(new Insets(20));

        card.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-color: #1E40AF;" +
                        "-fx-border-radius: 12;" +
                        "-fx-background-radius: 12;" +
                        "-fx-effect: dropshadow(gaussian, rgba(0,0,0,0.20), 10, 0, 0, 3);"
        );

        ImageView img;
        try {
            img = new ImageView(new Image(getClass().getResource(rutaImagen).toExternalForm()));
        } catch (Exception e) {
            img = new ImageView();
        }

        img.setFitWidth(150);
        img.setFitHeight(150);

        VBox info = new VBox(10);

        Label lblTitulo = new Label(titulo);
        lblTitulo.setStyle("-fx-font-size: 20px; -fx-font-weight: bold; -fx-text-fill:#1E3A8A;");

        Label lblAutor = new Label("Autor: " + autor);
        Label lblAnio = new Label("Año: " + anio);

        Label lblDesc = new Label(descripcion);
        lblDesc.setWrapText(true);
        lblDesc.setMaxWidth(450);

        HBox botones = new HBox(10);

        Button btnLeer = new Button("Leer");
        Button btnDescargar = new Button("Descargar");

        btnLeer.setStyle(
                "-fx-background-color: #2563EB; -fx-text-fill: white;" +
                        "-fx-font-weight: bold; -fx-background-radius: 8; -fx-padding: 6 14;"
        );

        btnDescargar.setStyle(
                "-fx-background-color: #1F3C88; -fx-text-fill: white;" +
                        "-fx-font-weight: bold; -fx-background-radius: 8; -fx-padding: 6 14;"
        );

        if (rol.equals("INVITADO")) {
            btnLeer.setDisable(true);
            btnDescargar.setDisable(true);
            btnLeer.setStyle("-fx-background-color: #777; -fx-text-fill: white;");
            btnDescargar.setStyle("-fx-background-color: #777; -fx-text-fill: white;");
        } else {
            btnLeer.setOnAction(e -> {
                Alert a = new Alert(Alert.AlertType.INFORMATION);
                a.setTitle("Lectura de artículo");
                a.setHeaderText("Abriendo artículo...");
                a.setContentText("Leyendo: " + titulo);
                a.showAndWait();
            });

            btnDescargar.setOnAction(e -> {
                Alert a = new Alert(Alert.AlertType.INFORMATION);
                a.setTitle("Descarga de artículo");
                a.setHeaderText("Descarga iniciada");
                a.setContentText("Descargando artículo: " + titulo);
                a.showAndWait();
            });
        }

        botones.getChildren().addAll(btnLeer, btnDescargar);

        info.getChildren().addAll(lblTitulo, lblAutor, lblAnio, lblDesc, botones);

        card.getChildren().addAll(img, info);

        return new VBox(card);
    }
}



package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;

public class DispositivosView {

    // =============================
    // LISTA TEMPORAL DE PRÉSTAMOS
    // =============================
    public static ArrayList<String> prestamosDispositivos = new ArrayList<>();


    public void mostrar(Stage stage, String rol) {

        VBox container = new VBox(25);
        container.setPadding(new Insets(30));
        container.setAlignment(Pos.TOP_CENTER);

        Label titulo = new Label("Dispositivos");
        titulo.setStyle("-fx-font-size: 26px; -fx-font-weight: bold; -fx-text-fill:#1E3A8A;");
        container.getChildren().add(titulo);

        container.getChildren().add(crearDispositivo("Laptop Dell XPS", "Intel i7 · 16GB · 512GB", "/img/laptop1.jpg", rol));
        container.getChildren().add(crearDispositivo("Laptop Lenovo ThinkPad", "Intel i5 · 8GB · 256GB", "/img/laptop2.jpg", rol));
        container.getChildren().add(crearDispositivo("Tablet Samsung", "Octa-Core · 4GB · 64GB", "/img/tablet1.jpg", rol));

        ScrollPane sp = new ScrollPane(container);
        sp.setFitToWidth(true);

        Scene scene = new Scene(sp, 950, 680);
        stage.setScene(scene);
        stage.setTitle("Dispositivos");
        stage.show();
    }



    // ============================================================
    // CREAR TARJETA DE DISPOSITIVO
    // ============================================================

    private HBox crearDispositivo(String nombre, String specs, String imgRuta, String rol) {

        HBox box = new HBox(20);
        box.setPadding(new Insets(20));
        box.setAlignment(Pos.CENTER_LEFT);

        // Estilo tarjeta moderno
        box.setStyle(
                "-fx-background-color: #FFFFFF;" +
                        "-fx-border-color: #1E40AF;" +
                        "-fx-border-radius: 12;" +
                        "-fx-background-radius: 12;" +
                        "-fx-effect: dropshadow(gaussian, rgba(0,0,0,0.20), 10, 0, 0, 3);"
        );

        // Animación Hover
        box.setOnMouseEntered(e ->
                box.setStyle(
                        "-fx-background-color: #F0F7FF;" +
                                "-fx-border-color: #1E40AF;" +
                                "-fx-border-radius: 12;" +
                                "-fx-background-radius: 12;" +
                                "-fx-effect: dropshadow(gaussian, rgba(0,0,0,0.28), 12, 0, 0, 4);"
                )
        );
        box.setOnMouseExited(e ->
                box.setStyle(
                        "-fx-background-color: #FFFFFF;" +
                                "-fx-border-color: #1E40AF;" +
                                "-fx-border-radius: 12;" +
                                "-fx-background-radius: 12;" +
                                "-fx-effect: dropshadow(gaussian, rgba(0,0,0,0.20), 10, 0, 0, 3);"
                )
        );

        // Imagen
        ImageView img;
        try {
            img = new ImageView(new Image(getClass().getResource(imgRuta).toExternalForm()));
        } catch (Exception ex) {
            img = new ImageView();
        }
        img.setFitWidth(130);
        img.setFitHeight(130);

        // Información del dispositivo
        VBox info = new VBox(10);
        Label lNombre = new Label(nombre);
        lNombre.setStyle("-fx-font-size: 18px; -fx-font-weight: bold; -fx-text-fill:#1E3A8A;");

        Label lSpecs = new Label(specs);
        lSpecs.setStyle("-fx-font-size: 14px;");

        // Botón moderno azul
        Button btnPrestar = new Button("Prestar");
        btnPrestar.setStyle(
                "-fx-background-color: #2563EB;" +
                        "-fx-text-fill: white;" +
                        "-fx-font-weight: bold;" +
                        "-fx-background-radius: 8;" +
                        "-fx-padding: 8 18;"
        );

        // Hover botón
        btnPrestar.setOnMouseEntered(e ->
                btnPrestar.setStyle(
                        "-fx-background-color: #1D4ED8;" +
                                "-fx-text-fill: white;" +
                                "-fx-font-weight: bold;" +
                                "-fx-background-radius: 8;" +
                                "-fx-padding: 8 18;"
                )
        );
        btnPrestar.setOnMouseExited(e ->
                btnPrestar.setStyle(
                        "-fx-background-color: #2563EB;" +
                                "-fx-text-fill: white;" +
                                "-fx-font-weight: bold;" +
                                "-fx-background-radius: 8;" +
                                "-fx-padding: 8 18;"
                )
        );

        // Restricción por rol
        if (rol.equalsIgnoreCase("INVITADO")) {
            btnPrestar.setDisable(true);
            btnPrestar.setText("Restringido");
            btnPrestar.setStyle("-fx-background-color: #9CA3AF; -fx-text-fill:white; -fx-background-radius: 8;");
        } else {

            // Acción principal del botón
            btnPrestar.setOnAction(e -> registrarPrestamoDispositivo(nombre));
        }

        info.getChildren().addAll(lNombre, lSpecs, btnPrestar);

        box.getChildren().addAll(img, info);
        return box;
    }



    // ============================================================
    // REGISTRO DE PRÉSTAMO (SIN BD)
    // ============================================================

    private void registrarPrestamoDispositivo(String nombreDispositivo) {

        LocalDate fecha = LocalDate.now();
        String fechaTexto = fecha.format(DateTimeFormatter.ofPattern("dd/MM/yyyy"));

        // Registrar temporalmente el préstamo
        String registro = "Dispositivo: " + nombreDispositivo + "  |  Fecha: " + fechaTexto;
        prestamosDispositivos.add(registro);

        // Mostrar mensaje
        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("Préstamo Registrado");
        alert.setHeaderText("Uso dentro del aula");
        alert.setContentText(
                "Se registró el préstamo del dispositivo.\n\n" +
                        "📌 Dispositivo: " + nombreDispositivo + "\n" +
                        "📅 Fecha: " + fechaTexto + "\n\n" +
                        "⚠ Este dispositivo SOLO puede utilizarse dentro del aula."
        );

        alert.showAndWait();
    }
}


package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class InstrumentoView {

    public void mostrar(Stage stage, String rol) {

        VBox root = new VBox(25);
        root.setAlignment(Pos.TOP_CENTER);
        root.setPadding(new Insets(30));
        root.setStyle("-fx-background-color: #F2F2F2;"); // ❗Fondo igual que Artículos Digitales

        Label titulo = new Label("Instrumentos");
        titulo.setStyle(
                "-fx-font-size: 28px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #1F3C88;" // ❗Azul del título
        );

        // Tarjetas de instrumentos
        VBox multimetro = crearInstrumentoCard("Multímetro Digital",
                "Precisión: ±0.8% • Rango automático • Pantalla LCD retroiluminada",
                "/img/multimetro.jpg", rol);

        VBox microscopio = crearInstrumentoCard("Microscopio Óptico",
                "Aumentos: 40x–1000x • Iluminación LED • Enfoque macro y micro",
                "/img/microscopio.jpg", rol);

        VBox proyector = crearInstrumentoCard("Proyector Epson",
                "Brillo: 3400 lúmenes • Resolución 1080p • Conectividad HDMI/VGA",
                "/img/proyector.jpg", rol);

        root.getChildren().addAll(titulo, multimetro, microscopio, proyector);

        Scene scene = new Scene(root, 1000, 650);
        stage.setScene(scene);
        stage.setTitle("Instrumentos");
        stage.show();
    }

    // ================================
    // TARJETA DE INSTRUMENTO ESTILO COMPLETO
    // ================================
    private VBox crearInstrumentoCard(String nombre, String detalles, String imgRuta, String rol) {

        VBox card = new VBox();
        card.setPadding(new Insets(20));
        card.setSpacing(10);
        card.setMaxWidth(900);

        // ❗ESTILO EXACTO IGUAL AL DE ARTÍCULOS DIGITALES
        card.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-color: #3B56A6;" +          // Azul del borde
                        "-fx-border-width: 2;" +
                        "-fx-border-radius: 12;" +
                        "-fx-background-radius: 12;" +
                        "-fx-effect: dropshadow(gaussian, #00000022, 8, 0, 0, 2);" +
                        "-fx-padding: 15;"
        );

        HBox cont = new HBox(20);
        cont.setAlignment(Pos.CENTER_LEFT);

        ImageView img;
        try {
            img = new ImageView(new Image(getClass().getResource(imgRuta).toExternalForm()));
        } catch (Exception e) {
            img = new ImageView();
        }

        img.setFitWidth(120);
        img.setFitHeight(120);
        img.setPreserveRatio(true);

        VBox info = new VBox(6);

        Label n = new Label(nombre);
        n.setStyle(
                "-fx-font-weight: bold;" +
                        "-fx-font-size: 20px;" +
                        "-fx-text-fill: #1F3C88;" // ❗Título azul igual
        );

        Label d = new Label(detalles);
        d.setStyle("-fx-font-size: 14px;");

        Button btnPrestar = new Button("Prestar");
        btnPrestar.setStyle(
                "-fx-background-color: #1F3C88;" +
                        "-fx-text-fill: white;" +
                        "-fx-padding: 10 20;" +
                        "-fx-background-radius: 6;" +
                        "-fx-font-size: 14px;"
        );

        // Hover button
        btnPrestar.setOnMouseEntered(e -> btnPrestar.setStyle(
                "-fx-background-color: #3B56A6;" +
                        "-fx-text-fill: white;" +
                        "-fx-padding: 10 20;" +
                        "-fx-background-radius: 6;" +
                        "-fx-font-size: 14px;"
        ));

        btnPrestar.setOnMouseExited(e -> btnPrestar.setStyle(
                "-fx-background-color: #1F3C88;" +
                        "-fx-text-fill: white;" +
                        "-fx-padding: 10 20;" +
                        "-fx-background-radius: 6;" +
                        "-fx-font-size: 14px;"
        ));

        if (rol.equalsIgnoreCase("INVITADO")) {
            btnPrestar.setDisable(true);
            btnPrestar.setText("Restringido");
            btnPrestar.setStyle(
                    "-fx-background-color: #cccccc;" +
                            "-fx-text-fill: white;" +
                            "-fx-padding: 10 20;" +
                            "-fx-background-radius: 6;"
            );
        } else {
            btnPrestar.setOnAction(e ->
                    System.out.println("Préstamo de instrumento: " + nombre)
            );
        }

        info.getChildren().addAll(n, d, btnPrestar);

        cont.getChildren().addAll(img, info);
        card.getChildren().add(cont);

        return card;
    }
}


package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.ScrollPane;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.VBox;
import javafx.scene.layout.HBox;
import javafx.stage.Stage;

public class LibrosDigitalesView {

    public void mostrar(Stage stage, String rol) {

        VBox container = new VBox(25);
        container.setPadding(new Insets(20));
        container.setAlignment(Pos.TOP_CENTER);
        container.setStyle("-fx-background-color: #F2F2F2;");

        Label titulo = new Label("Libros Digitales");
        titulo.setStyle(
                "-fx-font-size: 28px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #1F3C88;"
        );
        container.getChildren().add(titulo);

        container.getChildren().add(crearLibro("Fundamentos de Bases de Datos", "/img/libro1.jpg", rol));
        container.getChildren().add(crearLibro("Programación en Java", "/img/libro2.jpg", rol));
        container.getChildren().add(crearLibro("Redes de Computadoras", "/img/libro3.jpg", rol));
        container.getChildren().add(crearLibro("Inteligencia Artificial Moderna", "/img/libro4.jpg", rol));
        container.getChildren().add(crearLibro("Diseño de Sistemas Web", "/img/libro5.jpg", rol));
        container.getChildren().add(crearLibro("Ingeniería de Software", "/img/libro6.jpg", rol));
        container.getChildren().add(crearLibro("Estructuras de Datos", "/img/libro7.jpg", rol));
        container.getChildren().add(crearLibro("Ciberseguridad Avanzada", "/img/libro8.jpg", rol));

        ScrollPane sp = new ScrollPane(container);
        sp.setFitToWidth(true);

        Scene scene = new Scene(sp, 1100, 700);
        stage.setScene(scene);
        stage.setTitle("Libros Digitales");
        stage.show();
    }

    private HBox crearLibro(String nombre, String imgRuta, String rol) {

        HBox box = new HBox(20);
        box.setPadding(new Insets(15));
        box.setAlignment(Pos.CENTER_LEFT);

        box.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-color: #3B56A6;" +
                        "-fx-border-width: 2;" +
                        "-fx-border-radius: 12;" +
                        "-fx-background-radius: 12;" +
                        "-fx-effect: dropshadow(gaussian, #00000022, 8, 0, 0, 2);"
        );

        ImageView img;
        try {
            img = new ImageView(new Image(getClass().getResource(imgRuta).toExternalForm()));
        } catch (Exception ex) {
            img = new ImageView();
        }
        img.setFitWidth(120);
        img.setFitHeight(150);

        VBox info = new VBox(10);

        Label lbl = new Label(nombre);
        lbl.setStyle(
                "-fx-font-size: 20px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #1F3C88;"
        );

        HBox botones = new HBox(12);

        Button btnLeer = new Button("Leer");
        Button btnDescargar = new Button("Descargar");

        btnLeer.setStyle(
                "-fx-background-color: #2563EB; -fx-text-fill: white;" +
                        "-fx-font-weight: bold; -fx-background-radius: 8; -fx-padding: 6 16;"
        );

        btnDescargar.setStyle(
                "-fx-background-color: #1F3C88; -fx-text-fill: white;" +
                        "-fx-font-weight: bold; -fx-background-radius: 8; -fx-padding: 6 16;"
        );

        // ---- Restricción para INVITADO ----
        if (rol.equalsIgnoreCase("INVITADO")) {
            btnLeer.setDisable(true);
            btnDescargar.setDisable(true);
            btnLeer.setStyle("-fx-background-color: #777; -fx-text-fill: white;");
            btnDescargar.setStyle("-fx-background-color: #777; -fx-text-fill: white;");
        } else {
            btnLeer.setOnAction(e -> {
                Alert a = new Alert(Alert.AlertType.INFORMATION);
                a.setTitle("Leer libro digital");
                a.setHeaderText("Abriendo libro...");
                a.setContentText("Estás leyendo: " + nombre);
                a.showAndWait();
            });

            btnDescargar.setOnAction(e -> {
                Alert a = new Alert(Alert.AlertType.INFORMATION);
                a.setTitle("Descarga");
                a.setHeaderText("Descarga iniciada");
                a.setContentText("Se descargará: " + nombre);
                a.showAndWait();
            });
        }

        botones.getChildren().addAll(btnLeer, btnDescargar);

        info.getChildren().addAll(lbl, botones);
        box.getChildren().addAll(img, info);

        return box;
    }
}




import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

import java.time.LocalDate;
import java.time.temporal.ChronoUnit;
import java.util.*;

/**
 * LibrosFisicosView mejorado:
 * - control en memoria de prestamos por usuario
 * - control de deuda (si atraso > 7 dias)
 * - limite 2 prestamos simultaneos
 * - verificación de disponibilidad por recurso (título)
 * - avatar en la parte superior (usa archivo local subido)
 *
 * Nota: este control es en memoria; si cierras la app se pierde.
 */
public class LibrosFisicosView {

    // ---------------------------
    // Estado en memoria (global)
    // ---------------------------
    private static final Map<String, List<PrestamoRecord>> userLoans = new HashMap<>();
    private static final Set<String> usersWithDebt = new HashSet<>();
    private static final Map<String, Boolean> disponibilidad = new HashMap<>();

    static {
        disponibilidad.put("Estructura de Datos", true);
        disponibilidad.put("Fundamentos de Bases de Datos", true);
        disponibilidad.put("Clean Code", true);
        disponibilidad.put("The Mythical Man-Month", true);
        disponibilidad.put("The Pragmatic Programmer", true);
        disponibilidad.put("Code Complete (2nd Edition)", true);
        disponibilidad.put("Introduction to Algorithms", true);
        disponibilidad.put("Refactoring", true);
    }

    public void mostrar(Stage stage, String rol) {

        // Barra superior con rol + avatar
        HBox topBar = new HBox(12);
        topBar.setPadding(new Insets(12, 30, 12, 30));
        topBar.setAlignment(Pos.CENTER_RIGHT);

        String rolLabelText = rol == null ? "Rol: Invitado" : (rol.equalsIgnoreCase("NORMAL") ? "Rol: Alumno" : "Rol: " + rol);
        Label rolLabel = new Label(rolLabelText);
        rolLabel.setStyle("-fx-font-size: 14px; -fx-font-weight: bold; -fx-text-fill: #1E3A8A;");

        ImageView avatar = new ImageView();
        try {
            Image img = new Image("file:/mnt/data/A_flat_digital_illustration_features_a_simple,_sty.png");
            avatar.setImage(img);
        } catch (Exception ex) {
            System.out.println("⚠️ No se pudo cargar avatar desde file (fallback). " + ex.getMessage());
        }
        avatar.setFitWidth(34);
        avatar.setFitHeight(34);
        avatar.setPreserveRatio(true);

        topBar.getChildren().addAll(rolLabel, avatar);

        // CONTENIDO
        VBox contenido = new VBox(18);
        contenido.setPadding(new Insets(18));
        contenido.setAlignment(Pos.TOP_CENTER);
        contenido.setStyle("-fx-background-color: #F8FAFC;");

        Label titulo = new Label("Libros Físicos");
        titulo.setStyle(
                "-fx-font-size: 32px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #1E3A8A;"
        );

        contenido.getChildren().addAll(topBar, titulo);

        // Lista de tarjetas
        contenido.getChildren().add(crearLibroCard(
                "Estructura de Datos", "Narasimha Karumanchi",
                "ISBN: 978-9383242000", "Sistemas",
                "Libro fundamental para estructuras de datos.",
                "/img/libro_sistemas1.png", rol
        ));

        contenido.getChildren().add(crearLibroCard(
                "Fundamentos de Bases de Datos", "Héctor García-Molina",
                "ISBN: 978-8409012345", "Informática",
                "Libro avanzado sobre bases de datos.",
                "/img/libro_sistemas2.png", rol
        ));

        contenido.getChildren().add(crearLibroCard(
                "Clean Code", "Robert C. Martin",
                "ISBN: 978-0132350884", "Informática",
                "Principios y prácticas para escribir código limpio.",
                "/img/libro_sistemas3.png", rol
        ));

        contenido.getChildren().add(crearLibroCard(
                "The Mythical Man-Month", "Frederick Brooks",
                "ISBN: 978-0201835953", "Ingeniería de Software",
                "Ensayos clásicos sobre ingeniería de software.",
                "/img/libro_sistemas4.png", rol
        ));

        contenido.getChildren().add(crearLibroCard(
                "The Pragmatic Programmer", "David Thomas y Andrew Hunt",
                "ISBN: 978-0201616224", "Informática",
                "Consejos esenciales para programadores profesionales.",
                "/img/libro_sistemas5.png", rol
        ));

        contenido.getChildren().add(crearLibroCard(
                "Code Complete (2nd Edition)", "Steve McConnell",
                "ISBN: 978-0735619678", "Informática",
                "Guía exhaustiva para desarrollo profesional.",
                "/img/libro_sistemas6.png", rol
        ));

        contenido.getChildren().add(crearLibroCard(
                "Introduction to Algorithms",
                "Cormen, Leiserson, Rivest, Stein",
                "ISBN: 978-0262033848", "Informática",
                "Texto más completo sobre algoritmos.",
                "/img/libro_sistemas7.png", rol
        ));

        contenido.getChildren().add(crearLibroCard(
                "Refactoring", "Martin Fowler",
                "ISBN: 978-0201485677", "Informática",
                "Mejora el diseño del código existente.",
                "/img/libro_sistemas8.png", rol
        ));


        ScrollPane scroll = new ScrollPane(contenido);
        scroll.setFitToWidth(true);
        scroll.setStyle("-fx-background-color: transparent;");


        Scene scene = new Scene(scroll, 1100, 650);

        stage.setScene(scene);
        stage.setTitle("Libros Físicos");
        stage.show();
    }

    // ---------------------------
    // TARJETA DE LIBRO
    // ---------------------------
    private VBox crearLibroCard(String titulo, String autor, String isbn,
                                String categoria, String descripcion,
                                String rutaImg, String rol) {

        VBox card = new VBox(12);
        card.setPadding(new Insets(18));
        card.setMaxWidth(1000);
        card.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-color: #3B82F6;" +
                        "-fx-border-width: 2;" +
                        "-fx-border-radius: 16;" +
                        "-fx-background-radius: 16;" +
                        "-fx-effect: dropshadow(gaussian, #00000022, 10, 0, 0, 3);"
        );

        HBox fila = new HBox(18);
        fila.setAlignment(Pos.CENTER_LEFT);

        ImageView img;
        try {
            img = new ImageView(new Image(getClass().getResourceAsStream(rutaImg)));
        } catch (Exception ex) {
            img = new ImageView();
        }
        img.setFitHeight(140);
        img.setFitWidth(110);
        img.setPreserveRatio(true);

        VBox info = new VBox(6);

        Label lTitulo = new Label(titulo);
        lTitulo.setStyle("-fx-font-size: 20px; -fx-font-weight: bold; -fx-text-fill: #1E3A8A;");

        Label lAutor = new Label("Autor: " + autor);
        lAutor.setStyle("-fx-font-size:13px; -fx-text-fill:#334155;");

        Label lISBN = new Label(isbn);
        lISBN.setStyle("-fx-font-size:12px; -fx-text-fill:#475569;");

        Label lCategoria = new Label("Categoría: " + categoria);
        lCategoria.setStyle("-fx-font-size:12px; -fx-text-fill:#475569;");

        Label lDescripcion = new Label(descripcion);
        lDescripcion.setStyle("-fx-font-size:13px; -fx-text-fill:#64748B;");

        info.getChildren().addAll(lTitulo, lAutor, lISBN, lCategoria, lDescripcion);

        fila.getChildren().addAll(img, info);
        card.getChildren().add(fila);

        HBox acciones = new HBox(10);
        acciones.setAlignment(Pos.CENTER_LEFT);

        boolean disp = disponibilidad.getOrDefault(titulo, true);
        Label estadoLbl = new Label(disp ? "Disponible" : "No disponible");
        estadoLbl.setStyle(disp ? "-fx-text-fill: #059669; -fx-font-weight: bold;"
                : "-fx-text-fill: #DC2626; -fx-font-weight: bold;");

        if (!"INVITADO".equalsIgnoreCase(rol)) {
            Button btnAccion = new Button("Leer / Prestar");
            btnAccion.setStyle(
                    "-fx-background-color: linear-gradient(to right, #1E3A8A, #3B82F6);" +
                            "-fx-text-fill:white; -fx-padding: 10 16;" +
                            "-fx-font-size:14px; -fx-background-radius: 8;"
            );

            btnAccion.setOnAction(e -> mostrarOpcionesPrestamo(titulo));

            acciones.getChildren().addAll(btnAccion, estadoLbl);
        } else {
            acciones.getChildren().add(estadoLbl);
        }

        card.getChildren().add(acciones);
        return card;
    }

    // ---------------------------
    // DIALOGO LEER / PRESTAR
    // ---------------------------
    private void mostrarOpcionesPrestamo(String tituloRecurso) {

        Stage dialog = new Stage();
        dialog.setTitle("Opciones para " + tituloRecurso);

        VBox box = new VBox(16);
        box.setAlignment(Pos.CENTER);
        box.setPadding(new Insets(18));
        box.setStyle("-fx-background-color: #FFFFFF; -fx-border-radius: 12; -fx-background-radius:12;");

        Label pregunta = new Label("¿Qué desea hacer?");
        pregunta.setStyle("-fx-font-size: 16px; -fx-font-weight: bold; -fx-text-fill:#1E3A8A;");

        Button btnLeer = new Button("Leer en pantalla");
        Button btnPrestar = new Button("Pedir prestado (3 días)");

        btnLeer.setStyle("-fx-background-color:#2563EB; -fx-text-fill:white; -fx-background-radius:8; -fx-padding:8 14;");
        btnPrestar.setStyle("-fx-background-color:#1E40AF; -fx-text-fill:white; -fx-background-radius:8; -fx-padding:8 14;");

        btnLeer.setOnAction(ev -> {
            dialog.close();
            Alert info = new Alert(Alert.AlertType.INFORMATION);
            info.setTitle("Leer");
            info.setHeaderText(null);
            info.setContentText("Abriendo vista para leer: " + tituloRecurso);
            info.show();
        });

        btnPrestar.setOnAction(ev -> {
            dialog.close();
            solicitarYRegistrarPrestamo(tituloRecurso);
        });

        box.getChildren().addAll(pregunta, btnLeer, btnPrestar);

        Scene sc = new Scene(box, 360, 220);
        dialog.setScene(sc);
        dialog.show();
    }

    // ------------------------------------
    // PRÉSTAMOS EN MEMORIA
    // ------------------------------------
    private void solicitarYRegistrarPrestamo(String tituloRecurso) {

        TextInputDialog dialog = new TextInputDialog();
        dialog.setTitle("Solicitar ID de usuario");
        dialog.setHeaderText("Ingrese su ID de usuario");
        dialog.setContentText("ID:");

        Optional<String> result = dialog.showAndWait();
        if (result.isEmpty()) return;

        String idUsuario = result.get().trim();
        if (idUsuario.isEmpty()) {
            Alert a = new Alert(Alert.AlertType.ERROR);
            a.setContentText("Debe ingresar un ID válido.");
            a.show();
            return;
        }

        comprobarYMarcarDeuda(idUsuario);

        if (usersWithDebt.contains(idUsuario)) {
            Alert a = new Alert(Alert.AlertType.WARNING);
            a.setTitle("Deuda pendiente");
            a.setContentText("No puedes pedir un préstamo. Tienes una deuda pendiente.");
            a.show();
            return;
        }

        List<PrestamoRecord> prestamosActivos = userLoans.getOrDefault(idUsuario, new ArrayList<>());
        if (prestamosActivos.size() >= 2) {
            Alert a = new Alert(Alert.AlertType.WARNING);
            a.setTitle("Límite de préstamos");
            a.setContentText("Máximo 2 préstamos activos.");
            a.show();
            return;
        }

        boolean disp = disponibilidad.getOrDefault(tituloRecurso, true);
        if (!disp) {
            Alert a = new Alert(Alert.AlertType.ERROR);
            a.setTitle("No disponible");
            a.setContentText("Este libro no está disponible.");
            a.show();
            return;
        }

        LocalDate hoy = LocalDate.now();
        LocalDate fechaDevolucion = hoy.plusDays(3);
        PrestamoRecord pr = new PrestamoRecord(tituloRecurso, hoy, fechaDevolucion);

        prestamosActivos.add(pr);
        userLoans.put(idUsuario, prestamosActivos);

        disponibilidad.put(tituloRecurso, false);

        Alert a = new Alert(Alert.AlertType.INFORMATION);
        a.setTitle("Préstamo registrado");
        a.setHeaderText("Préstamo exitoso");
        a.setContentText("Disponible hasta: " + fechaDevolucion);
        a.show();
    }

    // Marca deuda si hay atraso > 7 días
    private void comprobarYMarcarDeuda(String idUsuario) {
        List<PrestamoRecord> prestamos = userLoans.getOrDefault(idUsuario, Collections.emptyList());
        for (PrestamoRecord p : prestamos) {
            long diasAtraso = ChronoUnit.DAYS.between(p.fechaDevolucion, LocalDate.now());
            if (diasAtraso > 7) {
                usersWithDebt.add(idUsuario);
                return;
            }
        }
        usersWithDebt.remove(idUsuario);
    }

    // Registro simple
    private static class PrestamoRecord {
        String recurso;
        LocalDate fechaInicio;
        LocalDate fechaDevolucion;

        PrestamoRecord(String recurso, LocalDate fechaInicio, LocalDate fechaDevolucion) {
            this.recurso = recurso;
            this.fechaInicio = fechaInicio;
            this.fechaDevolucion = fechaDevolucion;
        }
    }
}



package sgbu;

import javafx.fxml.FXMLLoader;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.effect.BoxBlur;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.StackPane;
import javafx.scene.layout.VBox;
import javafx.scene.paint.Color;
import javafx.scene.text.Font;
import javafx.stage.Stage;
import javafx.util.Duration;
import javafx.animation.FadeTransition;
import sgbu.DAO.LoginDAO;
import sgbu.DAO.UsuarioDAO;
import sgbu.MODEL.Usuario;

public class LoginView {

    public void mostrar(Stage stage) {

        Image image = new Image(getClass().getResource("/img/biblioteca.jpg").toExternalForm());
        ImageView background = new ImageView(image);
        background.setFitWidth(900);
        background.setFitHeight(600);
        background.setPreserveRatio(false);
        background.setEffect(new BoxBlur(8, 8, 3));

        StackPane root = new StackPane();

        VBox card = new VBox(12);
        card.setAlignment(Pos.CENTER);
        card.setPadding(new Insets(24));
        card.setPrefWidth(360);
        card.setStyle("-fx-background-color: rgba(255,255,255,0.94); -fx-background-radius: 12; -fx-border-color:#DDD; -fx-border-radius:12;");

        Label titulo = new Label("Biblioteca");
        titulo.setFont(new Font("Arial", 28));
        titulo.setTextFill(Color.web("#333"));

        TextField txtUsuario = new TextField();
        txtUsuario.setPromptText("Correo electrónico");
        txtUsuario.setStyle("-fx-background-color: white; -fx-border-color: #ccc; -fx-border-radius: 4; -fx-padding: 8;");

        PasswordField txtPassword = new PasswordField();
        txtPassword.setPromptText("Contraseña");
        txtPassword.setStyle("-fx-background-color: white; -fx-border-color: #ccc; -fx-border-radius: 4; -fx-padding: 8;");

        Button btnLogin = new Button("Iniciar sesión");
        btnLogin.setPrefWidth(260);
        btnLogin.setStyle("-fx-background-color: #007bff; -fx-text-fill: white; -fx-border-radius: 4; -fx-padding: 10;");

        Button btnInvitado = new Button("Entrar como invitado");
        btnInvitado.setPrefWidth(260);
        btnInvitado.setStyle("-fx-background-color: #6c757d; -fx-text-fill: white; -fx-border-radius: 4; -fx-padding: 10;");

        UsuarioDAO dao = new UsuarioDAO();

        btnLogin.setOnAction(e -> {
            String correo = txtUsuario.getText().trim();
            String pwd = txtPassword.getText();

            // Validar que no estén vacíos
            if (correo.isEmpty() || pwd.isEmpty()) {
                Alert a = new Alert(Alert.AlertType.WARNING, "Por favor ingrese correo y contraseña.");
                a.show();
                return;
            }

            LoginDAO loginDao = new LoginDAO();  // ✅ Usar LoginDAO, no UsuarioDAO
            Usuario u = loginDao.login(correo, pwd);  // ✅ Pasar correo y password

            if (u == null) {
                Alert a = new Alert(Alert.AlertType.ERROR, "Credenciales inválidas.");
                a.show();
                return;
            }

            if (u instanceof sgbu.MODEL.Bibliotecario) {
                abrirBibliotecario(stage);
                return;
            }

            new MenuPrincipalView().mostrar(stage, u);
        });

        btnInvitado.setOnAction(e -> {
            LoginDAO loginDao = new LoginDAO();
            Usuario invitado = loginDao.login("invitado@biblioteca.com", "invitado123");

            if (invitado == null) {
                invitado = new sgbu.MODEL.Invitado(
                        "guest",
                        "Invitado",
                        "guest@local",
                        "",
                        "guest",
                        "temporal",
                        java.time.LocalDate.now().plusDays(1),
                        60
                );
            }
            new MenuPrincipalView().mostrar(stage, invitado);
        });

        card.getChildren().addAll(titulo, txtUsuario, txtPassword, btnLogin, btnInvitado);
        root.getChildren().addAll(background, card);

        Scene scene = new Scene(root, 900, 600);

        // ✅ SOLUCIÓN COMPLETA: Usar setUserAgentStylesheet con valor null
        // Esto evita que JavaFX intente cargar recursos CSS internos
        javafx.application.Application.setUserAgentStylesheet(null);

        stage.setScene(scene);
        stage.show();

        FadeTransition fade = new FadeTransition(Duration.seconds(1.2), root);
        fade.setFromValue(0);
        fade.setToValue(1);
        fade.play();
    }

    private void abrirBibliotecario(Stage stage) {

        sgbu.CONTROLLER.BibliotecarioViewController controller = new sgbu.CONTROLLER.BibliotecarioViewController();
        controller.mostrar(stage);
    }

    private void abrirVentana(String rutaFxml, String titulo) {
        try {
            System.out.println("=== INTENTANDO ABRIR VENTANA ===");
            System.out.println("Ruta FXML: " + rutaFxml);

            // Verificar si el recurso existe
            java.net.URL recursoURL = getClass().getResource(rutaFxml);
            System.out.println("URL del recurso: " + recursoURL);

            if (recursoURL == null) {
                throw new Exception("El archivo FXML no se encontró en la ruta: " + rutaFxml);
            }

            FXMLLoader loader = new FXMLLoader(recursoURL);
            Parent root = loader.load();

            Stage stage = new Stage();
            stage.setScene(new Scene(root, 800, 600));
            stage.setTitle(titulo);
            stage.show();

            System.out.println("✅ Ventana abierta exitosamente");

        } catch (Exception e) {
            System.err.println("❌ ERROR DETALLADO:");
            e.printStackTrace();


            String mensajeDetallado = "Error al cargar: " + rutaFxml + "\n\n";
            mensajeDetallado += "Causa: " + e.getClass().getSimpleName() + "\n";
            mensajeDetallado += "Mensaje: " + e.getMessage();

            if (e.getCause() != null) {
                mensajeDetallado += "\n\nCausa raíz: " + e.getCause().getMessage();
            }

            Alert alert = new Alert(Alert.AlertType.ERROR);
            alert.setTitle("Error");
            alert.setHeaderText("No se pudo abrir la ventana");
            alert.setContentText(mensajeDetallado);
            alert.showAndWait();
        }
    }

}




package sgbu;

import javafx.animation.ScaleTransition;
import javafx.application.Application;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.layout.*;
import javafx.stage.Stage;
import javafx.util.Duration;
import sgbu.MODEL.Usuario;

public class MenuPrincipalView {

    public void mostrar(Stage stage, String rol) {
        // ✅ AGREGAR AL INICIO - Evitar IllegalAccessError
        Application.setUserAgentStylesheet(null);

        BorderPane root = new BorderPane();
        root.setStyle("-fx-background-color: linear-gradient(to bottom right, #f8fafc, #eef2f6);");

        // ----------------------------
        //     🔹 BARRA SUPERIOR
        // ----------------------------
        HBox topBar = new HBox();
        topBar.setAlignment(Pos.CENTER_RIGHT);
        topBar.setPadding(new Insets(10, 20, 10, 20));
        topBar.setSpacing(15);
        topBar.setStyle("-fx-background-color: white; -fx-border-color: #d8dce3; -fx-border-width: 0 0 1 0;");

        // Rol mostrado
        String rolMostrar = rol.equalsIgnoreCase("INVITADO") ? "Invitado" : "Alumno";
        Label lblRol = new Label("Rol: " + rolMostrar);
        lblRol.setStyle("-fx-font-size: 14px; -fx-text-fill: #4a5568;");

        // Botón cerrar sesión
        Button btnCerrar = new Button("Cerrar sesión");
        btnCerrar.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-color: #cbd5e1;" +
                        "-fx-border-radius: 8;" +
                        "-fx-background-radius: 8;" +
                        "-fx-padding: 6 14;" +
                        "-fx-cursor: hand;"
        );

        btnCerrar.setOnAction(e -> new LoginView().mostrar(stage));

        topBar.getChildren().addAll(lblRol, btnCerrar);
        root.setTop(topBar);

        // ----------------------------
        //     🔹 BOTONES CENTRALES
        // ----------------------------
        VBox centerBox = new VBox(20);
        centerBox.setAlignment(Pos.CENTER);

        Button btnRecursosFisicos = crearBotonAnimado("Recursos Físicos");
        Button btnRecursosDigitales = crearBotonAnimado("Recursos Digitales");
        Button btnAmbientes = crearBotonAnimado("Ambientes y Salas");
        Button btnPenalizaciones = crearBotonAnimado("Penalizaciones");

        // Eventos originales
        btnRecursosFisicos.setOnAction(e -> new RecursosFisicosView().mostrar(stage, rol));
        btnRecursosDigitales.setOnAction(e -> new RecursosDigitalesView().mostrar(stage, rol));
        btnAmbientes.setOnAction(e -> new AmbientesView().mostrar(stage, rol));
        btnPenalizaciones.setOnAction(e -> new PenalizacionesView().mostrar(stage, rol));

        centerBox.getChildren().addAll(
                btnRecursosFisicos,
                btnRecursosDigitales,
                btnAmbientes,
                btnPenalizaciones
        );

        root.setCenter(centerBox);

        // Escena
        Scene scene = new Scene(root, 900, 600);
        stage.setScene(scene);
        stage.setTitle("Menú Principal – Biblioteca");
        stage.show();
    }

    // ---------------------------------------------------
    //        🔹 FUNCIÓN PARA CREAR BOTONES ANIMADOS
    // ---------------------------------------------------
    private Button crearBotonAnimado(String texto) {
        Button btn = new Button(texto);

        btn.setStyle(
                "-fx-font-size: 15px;" +
                        "-fx-background-color: white;" +
                        "-fx-border-color: #d0d7e2;" +
                        "-fx-border-radius: 12;" +
                        "-fx-background-radius: 12;" +
                        "-fx-padding: 12 30;" +
                        "-fx-cursor: hand;" +
                        "-fx-text-fill: #333;"
        );

        // Animación hover tipo "bounce"
        btn.setOnMouseEntered(e -> animarBoton(btn, 1.0, 1.05));
        btn.setOnMouseExited(e -> animarBoton(btn, 1.05, 1.0));

        return btn;
    }

    private void animarBoton(Button btn, double inicio, double fin) {
        ScaleTransition st = new ScaleTransition(Duration.millis(180), btn);
        st.setFromX(inicio);
        st.setFromY(inicio);
        st.setToX(fin);
        st.setToY(fin);
        st.play();
    }

    public void mostrar(Stage stage, Usuario usuario) {
        // ✅ AGREGAR AL INICIO - Evitar IllegalAccessError
        Application.setUserAgentStylesheet(null);

        BorderPane root = new BorderPane();
        root.setPadding(new Insets(20));

        Label rolLabel = new Label("Rol: " + (usuario == null ? "Invitado" : mapRol(usuario)));
        rolLabel.setStyle("-fx-font-size: 14px; -fx-font-weight: bold;");

        HBox top = new HBox();
        top.setAlignment(Pos.CENTER_RIGHT);
        top.getChildren().add(rolLabel);
        root.setTop(top);

        VBox center = new VBox(12);
        center.setAlignment(Pos.CENTER);
        center.setPadding(new Insets(40));

        Button btnRecursosFisicos = new Button("Recursos Físicos");
        Button btnRecursosDigitales = new Button("Recursos Digitales");
        Button btnAmbientes = new Button("Ambientes y Salas");
        Button btnPenalizaciones = new Button("Penalizaciones");

        btnRecursosFisicos.setOnAction(e -> new RecursosFisicosView().mostrar(stage, usuario.getRol()));
        btnRecursosDigitales.setOnAction(e -> new RecursosDigitalesView().mostrar(stage, usuario.getRol()));
        btnAmbientes.setOnAction(e -> new AmbientesView().mostrar(stage, usuario.getRol()));
        btnPenalizaciones.setOnAction(e -> new PenalizacionesView().mostrar(stage, usuario.getRol()));

        center.getChildren().addAll(btnRecursosFisicos, btnRecursosDigitales, btnAmbientes, btnPenalizaciones);
        root.setCenter(center);

        Scene s = new Scene(root, 800, 600);
        stage.setScene(s);
        stage.setTitle("Menú Principal – Biblioteca");
        stage.show();
    }

    private String mapRol(Usuario u) {
        if (u == null) return "Invitado";
        if ("NORMAL".equalsIgnoreCase(u.getRol()) || "alumno".equalsIgnoreCase(u.getRol())) return "Alumno";
        return u.getRol();
    }
}




package sgbu;

import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.fxml.FXML;
import javafx.scene.control.*;
import javafx.scene.control.cell.PropertyValueFactory;

import java.time.LocalDate;

public class MisPrestamosController {

    @FXML
    private TableView<Prestamo> tablaPrestamos;

    @FXML
    private TableColumn<Prestamo, String> colRecurso;

    @FXML
    private TableColumn<Prestamo, String> colTipo;

    @FXML
    private TableColumn<Prestamo, LocalDate> colFechaPrestamo;

    @FXML
    private TableColumn<Prestamo, LocalDate> colFechaLimite;

    @FXML
    private TableColumn<Prestamo, String> colEstado;

    @FXML
    private TableColumn<Prestamo, Button> colAccion;

    private ObservableList<Prestamo> listaPrestamos;

    @FXML
    public void initialize() {

        colRecurso.setCellValueFactory(new PropertyValueFactory<>("recurso"));
        colTipo.setCellValueFactory(new PropertyValueFactory<>("tipo"));
        colFechaPrestamo.setCellValueFactory(new PropertyValueFactory<>("fechaPrestamo"));
        colFechaLimite.setCellValueFactory(new PropertyValueFactory<>("fechaLimite"));
        colEstado.setCellValueFactory(new PropertyValueFactory<>("estado"));
        colAccion.setCellValueFactory(new PropertyValueFactory<>("btnDevolver"));

        // Datos simulados (se reemplazarán por la BD cuando estés lista)
        listaPrestamos = FXCollections.observableArrayList(
                new Prestamo("Libro X", "Físico", LocalDate.now(), LocalDate.now().plusDays(3)),
                new Prestamo("Laptop A", "Dispositivo", LocalDate.now(), LocalDate.now().plusDays(3))
        );

        tablaPrestamos.setItems(listaPrestamos);
    }

    // =========================
    // CLASE MODELO DEL PRESTAMO
    // =========================
    public static class Prestamo {
        private String recurso;
        private String tipo;
        private LocalDate fechaPrestamo;
        private LocalDate fechaLimite;
        private String estado;
        private Button btnDevolver;

        public Prestamo(String recurso, String tipo, LocalDate fechaPrestamo, LocalDate fechaLimite) {
            this.recurso = recurso;
            this.tipo = tipo;
            this.fechaPrestamo = fechaPrestamo;
            this.fechaLimite = fechaLimite;
            this.estado = "Prestado";

            this.btnDevolver = new Button("DEVOLVER");
            this.btnDevolver.setOnAction(e -> devolver());
        }

        // MÉTODO PARA DEVOLVER
        private void devolver() {

            Alert confirm = new Alert(Alert.AlertType.CONFIRMATION);
            confirm.setTitle("Confirmar devolución");
            confirm.setHeaderText("¿Deseas devolver este recurso?");
            confirm.setContentText("Recurso: " + recurso);

            if (confirm.showAndWait().get() == ButtonType.OK) {

                this.estado = "Devuelto";
                this.btnDevolver.setDisable(true);

                Alert ok = new Alert(Alert.AlertType.INFORMATION);
                ok.setTitle("Recurso devuelto");
                ok.setHeaderText(null);
                ok.setContentText("Se registró la devolución correctamente.\nFecha: " + LocalDate.now());
                ok.show();

                // Aquí luego agregarás tu UPDATE a BD
            }
        }

        // GETTERS
        public String getRecurso() { return recurso; }
        public String getTipo() { return tipo; }
        public LocalDate getFechaPrestamo() { return fechaPrestamo; }
        public LocalDate getFechaLimite() { return fechaLimite; }
        public String getEstado() { return estado; }
        public Button getBtnDevolver() { return btnDevolver; }
    }
}




package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.layout.*;
import javafx.stage.Stage;

public class PenalizacionesView {

    public void mostrar(Stage stage, String rol) {

        // ------- Fondo general -------
        BorderPane root = new BorderPane();
        root.setStyle("-fx-background-color: #E9F5FF;"); // azul muy suave

        // ------- Contenedor principal (tarjeta) -------
        VBox card = new VBox(20);
        card.setAlignment(Pos.CENTER_LEFT);
        card.setPadding(new Insets(25));
        card.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-radius: 15;" +
                        "-fx-background-radius: 15;" +
                        "-fx-border-color: #2C73D2;" +      // azul fuerte
                        "-fx-border-width: 2;"
        );

        // ------- Título -------
        Label titulo = new Label("Penalización por Retraso");
        titulo.setStyle("-fx-font-size: 24px; -fx-font-weight: bold; -fx-text-fill: #2C73D2;");

        // ------- Subtítulos y textos -------
        Label datosPrestamo = new Label("Datos del Préstamo");
        datosPrestamo.setStyle("-fx-font-size: 18px; -fx-font-weight: bold; -fx-text-fill: #1B6CA8;");

        Label diasLabel = new Label("Días de Retraso: [X]");
        Label tarifaLabel = new Label("Tarifa por Día: 1.5");

        Label totalLabel = new Label("Monto Total a Pagar: [X * 1.5]");
        totalLabel.setStyle("-fx-font-size: 18px; -fx-font-weight: bold; -fx-text-fill: #1E8449;"); // verde

        diasLabel.setStyle("-fx-font-size: 15px;");
        tarifaLabel.setStyle("-fx-font-size: 15px;");

        // ------- Botones -------
        Button btnPagar = new Button("Registrar Pago");
        btnPagar.setStyle(
                "-fx-background-color: #1E8449;" +  // verde
                        "-fx-text-fill: white;" +
                        "-fx-font-size: 14px;" +
                        "-fx-background-radius: 10;" +
                        "-fx-padding: 10 20;"
        );

        Button btnNotificar = new Button("Notificar Usuario");
        btnNotificar.setStyle(
                "-fx-background-color: #2C73D2;" + // azul
                        "-fx-text-fill: white;" +
                        "-fx-font-size: 14px;" +
                        "-fx-background-radius: 10;" +
                        "-fx-padding: 10 20;"
        );

        // ------- EVENTOS DE BOTONES -------
        btnPagar.setOnAction(e -> {
            Alert alert = new Alert(Alert.AlertType.INFORMATION);
            alert.setTitle("Pago Registrado");
            alert.setHeaderText("Registro Exitoso");
            alert.setContentText("El pago de la penalización ha sido registrado correctamente.");
            alert.showAndWait();
        });

        btnNotificar.setOnAction(e -> {
            Alert alert = new Alert(Alert.AlertType.INFORMATION);
            alert.setTitle("Notificación Enviada");
            alert.setHeaderText("Usuario Notificado");
            alert.setContentText("Se ha enviado la notificación correctamente al usuario.");
            alert.showAndWait();
        });

        // ------- Contenedor botones -------
        HBox botones = new HBox(15, btnPagar, btnNotificar);
        botones.setAlignment(Pos.CENTER);

        // ------- Añadir contenido a la tarjeta -------
        card.getChildren().addAll(
                titulo,
                datosPrestamo,
                diasLabel,
                tarifaLabel,
                totalLabel,
                botones
        );

        // ------- Centrar tarjeta -------
        BorderPane.setAlignment(card, Pos.CENTER);
        root.setCenter(card);
        root.setPadding(new Insets(50));

        // Mostrar escena
        stage.setScene(new Scene(root, 900, 650));
        stage.show();
    }
}




package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.image.ImageView;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;
import javafx.animation.TranslateTransition;
import javafx.util.Duration;

public class RecursosDigitalesView {

    public void mostrar(Stage stage, String rol) {

        BorderPane root = new BorderPane();
        root.setPadding(new Insets(20));

        // ░░░ BARRA SUPERIOR (ROL + AVATAR) ░░░
        HBox topBar = new HBox();
        topBar.setPadding(new Insets(10, 25, 10, 25));
        topBar.setAlignment(Pos.CENTER_RIGHT);
        topBar.setSpacing(8);

        Label rolLabel = new Label("Rol: " + rol);
        rolLabel.setStyle("-fx-font-size: 16px; -fx-font-weight: bold; -fx-text-fill: #2b2b2b;");

        // Avatar estilo modelo (emoji)
        Label avatar = new Label("👤");
        avatar.setStyle("-fx-font-size: 20px;");

        // Animación hover (rebote)
        TranslateTransition bounce = new TranslateTransition(Duration.millis(250), avatar);
        bounce.setFromY(0);
        bounce.setToY(-5);

        avatar.setOnMouseEntered(e -> {
            bounce.setRate(1);
            bounce.playFromStart();
        });
        avatar.setOnMouseExited(e -> {
            bounce.setRate(-1);
            bounce.play();
        });

        topBar.getChildren().addAll(rolLabel, avatar);
        root.setTop(topBar);

        // ░░░ TÍTULO (MISMO TAMAÑO DEL MODELO) ░░░
        Label titulo = new Label("📚 Recursos Digitales");
        titulo.setStyle("-fx-font-size: 28px; -fx-font-weight: bold; -fx-text-fill: #1b2a4a;");

        // ░░░ ESTILO DE BOTONES (MISMO MODELO QUE RECURSOS FÍSICOS) ░░░
        String botonBase =
                "-fx-background-color: white;" +
                        "-fx-padding: 14 32;" +
                        "-fx-background-radius: 14;" +
                        "-fx-border-radius: 14;" +
                        "-fx-border-color: #d0d0d0;" +
                        "-fx-font-size: 15px;" +
                        "-fx-alignment: CENTER_LEFT;" +
                        "-fx-text-fill: #1b2a4a;";

        // ICONOS ----------------------------------------------------------------

        ImageView iconLibros = new ImageView("https://cdn-icons-png.flaticon.com/512/29/29302.png");
        iconLibros.setFitWidth(18); iconLibros.setFitHeight(18);

        ImageView iconArticulos = new ImageView("https://cdn-icons-png.flaticon.com/512/2991/2991108.png");
        iconArticulos.setFitWidth(18); iconArticulos.setFitHeight(18);

        ImageView iconTesis = new ImageView("https://cdn-icons-png.flaticon.com/512/3135/3135673.png");
        iconTesis.setFitWidth(18); iconTesis.setFitHeight(18);

        // BOTONES ---------------------------------------------------------------

        Button btnLibrosDigitales = new Button("    Libros Digitales", iconLibros);
        btnLibrosDigitales.setStyle(botonBase);
        btnLibrosDigitales.setOnAction(e -> new LibrosDigitalesView().mostrar(stage, rol));

        Button btnArticulos = new Button("    Artículos Digitales", iconArticulos);
        btnArticulos.setStyle(botonBase);
        btnArticulos.setOnAction(e -> new ArticulosDigitalesView().mostrar(stage, rol));

        Button btnTesis = new Button("    Tesis Digitales", iconTesis);
        btnTesis.setStyle(botonBase);
        btnTesis.setOnAction(e -> new TesisDigitalesView().mostrar(stage, rol));

        // REBOTE ---------------------------------------------------------------

        agregarRebote(btnLibrosDigitales);
        agregarRebote(btnArticulos);
        agregarRebote(btnTesis);

        // PERMISOS INVITADO ----------------------------------------------------

        if (rol.equalsIgnoreCase("INVITADO")) {
            btnArticulos.setDisable(true);
            btnTesis.setDisable(true);
        }

        // CENTRO (MISMA ESTRUCTURA Y ESPACIADO DEL MODELO RECURSOS FÍSICOS) ----

        VBox centro = new VBox(22);
        centro.setAlignment(Pos.CENTER);
        centro.getChildren().addAll(titulo, btnLibrosDigitales, btnArticulos, btnTesis);

        root.setCenter(centro);

        Scene scene = new Scene(root, 900, 600);
        scene.setFill(javafx.scene.paint.Color.web("#ECF2FF"));

        stage.setScene(scene);
        stage.setTitle("Recursos Digitales");
        stage.show();
    }

    // REBOTE REUTILIZABLE PARA BOTONES
    private void agregarRebote(Button btn) {
        TranslateTransition t = new TranslateTransition(Duration.millis(120), btn);
        t.setFromY(0);
        t.setToY(-4);

        btn.setOnMouseEntered(e -> {
            t.setRate(1);
            t.playFromStart();
        });
        btn.setOnMouseExited(e -> {
            t.setRate(-1);
            t.play();
        });
    }
}





import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.effect.DropShadow;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.scene.paint.Color;
import javafx.stage.Stage;
import javafx.util.Duration;

public class RecursosFisicosView {

    public void mostrar(Stage stage, String rol) {

        // -------- LAYOUT BASE (SIN LA TARJETA BLANCA) --------
        BorderPane root = new BorderPane();
        root.setStyle("-fx-background-color: linear-gradient(to bottom, #e8eef5, #ffffff);");

        // -------- BARRA SUPERIOR: ROL + ICONO --------
        HBox barraSuperior = new HBox(10);
        barraSuperior.setPadding(new Insets(20, 30, 20, 0));
        barraSuperior.setAlignment(Pos.CENTER_RIGHT);

        Label rolLabel = new Label("Rol: " + rol);
        rolLabel.setStyle("-fx-font-size: 16px; -fx-text-fill: #1F2A44; -fx-font-weight: bold;");

        Label avatar = new Label("👤");
        avatar.setStyle("-fx-font-size: 22px;");

        // Animación avatar
        ScaleTransition avatarAnim = new ScaleTransition(Duration.millis(200), avatar);
        avatar.setOnMouseEntered(e -> {
            avatarAnim.setToX(1.2);
            avatarAnim.setToY(1.2);
            avatarAnim.playFromStart();
        });
        avatar.setOnMouseExited(e -> {
            avatarAnim.setToX(1.0);
            avatarAnim.setToY(1.0);
            avatarAnim.playFromStart();
        });

        barraSuperior.getChildren().addAll(rolLabel, avatar);
        root.setTop(barraSuperior);

        // -------- TITULO --------
        Label titulo = new Label("📚  Recursos Físicos");
        titulo.setStyle("-fx-font-size: 28px; -fx-font-weight: bold; -fx-text-fill: #2E3A59;");

        // -------- BOTONES --------
        Button btnLibros = crearBoton("📘   Libros Físicos");
        Button btnRevistas = crearBoton("📰   Revistas");
        Button btnDispositivos = crearBoton("💻   Dispositivos (Laptop / Tablet)");
        Button btnInstrumentos = crearBoton("🔬   Instrumentos");

        // -------- EVENTOS --------
        btnLibros.setOnAction(e -> new LibrosFisicosView().mostrar(stage, rol));
        btnRevistas.setOnAction(e -> new RevistasView().mostrar(stage, rol));
        btnDispositivos.setOnAction(e -> new DispositivosView().mostrar(stage, rol));
        btnInstrumentos.setOnAction(e -> new InstrumentoView().mostrar(stage, rol));

        // -------- CONTENEDOR CENTRAL SIN FONDO BLANCO --------
        VBox centerBox = new VBox(20);
        centerBox.setAlignment(Pos.CENTER);
        centerBox.getChildren().addAll(titulo, btnLibros, btnRevistas, btnDispositivos, btnInstrumentos);

        root.setCenter(centerBox);

        // -------- ESCENA --------
        Scene escena = new Scene(root, 1000, 700);
        stage.setScene(escena);
        stage.setTitle("Recursos Físicos");
        stage.show();
    }

    // ======================================================
    //  BOTÓN MODERNO + ANIMACIÓN HOVER + SOMBRA SUAVE
    // ======================================================
    private Button crearBoton(String texto) {
        Button btn = new Button(texto);
        btn.setPrefWidth(320);
        btn.setStyle(
                "-fx-font-size: 16px;" +
                        "-fx-padding: 12 20;" +
                        "-fx-background-color: #ffffff;" +
                        "-fx-background-radius: 10;" +
                        "-fx-text-fill: #2E3A59;" +
                        "-fx-border-color: #E0E6ED;" +
                        "-fx-border-radius: 10;" +
                        "-fx-border-width: 1;"
        );

        // Sombra suave
        btn.setEffect(new DropShadow(5, Color.rgb(0, 0, 0, 0.15)));

        // Animaciones
        ScaleTransition stHover = new ScaleTransition(Duration.millis(150), btn);
        stHover.setToX(1.05);
        stHover.setToY(1.05);

        ScaleTransition stNormal = new ScaleTransition(Duration.millis(150), btn);
        stNormal.setToX(1.0);
        stNormal.setToY(1.0);

        btn.setOnMouseEntered(e -> {
            btn.setStyle(
                    "-fx-font-size: 16px;" +
                            "-fx-padding: 12 20;" +
                            "-fx-background-color: #E8EEF5;" +
                            "-fx-background-radius: 10;" +
                            "-fx-text-fill: #2E3A59;" +
                            "-fx-border-color: #C8D0D9;" +
                            "-fx-border-radius: 10;" +
                            "-fx-border-width: 1;"
            );
            stHover.playFromStart();
        });

        btn.setOnMouseExited(e -> {
            btn.setStyle(
                    "-fx-font-size: 16px;" +
                            "-fx-padding: 12 20;" +
                            "-fx-background-color: #ffffff;" +
                            "-fx-background-radius: 10;" +
                            "-fx-text-fill: #2E3A59;" +
                            "-fx-border-color: #E0E6ED;" +
                            "-fx-border-radius: 10;" +
                            "-fx-border-width: 1;"
            );
            stNormal.playFromStart();
        });

        return btn;
    }
} 




package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.ScrollPane;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class RevistasView {

    public void mostrar(Stage stage, String rol) {

        VBox container = new VBox(25);
        container.setPadding(new Insets(30));
        container.setAlignment(Pos.TOP_CENTER);
        container.setStyle("-fx-background-color: #F2F2F2;"); // Fondo igual que Artículos

        Label titulo = new Label("Revistas");
        titulo.setStyle(
                "-fx-font-size: 28px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #1F3C88;" // Azul del título
        );
        container.getChildren().add(titulo);

        // Revistas
        container.getChildren().add(crearRevista("Sistemas complejos e inteligentes", "2024", "/img/revista1.jpg", rol));
        container.getChildren().add(crearRevista("Revista de Ingeniería", "2022", "/img/revista2.jpg", rol));
        container.getChildren().add(crearRevista("Revista Científica", "2022", "/img/revista3.jpg", rol));

        ScrollPane sp = new ScrollPane(container);
        sp.setFitToWidth(true);

        Scene scene = new Scene(sp, 1000, 650);
        stage.setScene(scene);
        stage.setTitle("Revistas");
        stage.show();
    }

    private VBox crearRevista(String titulo, String anio, String imgRuta, String rol) {

        VBox card = new VBox();
        card.setPadding(new Insets(20));
        card.setSpacing(10);
        card.setMaxWidth(900);

        // 📌 Estilo idéntico a Artículos Digitales
        card.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-color: #3B56A6;" +
                        "-fx-border-width: 2;" +
                        "-fx-border-radius: 12;" +
                        "-fx-background-radius: 12;" +
                        "-fx-effect: dropshadow(gaussian, #00000022, 8, 0, 0, 2);" +
                        "-fx-padding: 15;"
        );

        HBox box = new HBox(20);
        box.setAlignment(Pos.CENTER_LEFT);

        ImageView img;
        try {
            img = new ImageView(new Image(getClass().getResource(imgRuta).toExternalForm()));
        } catch (Exception ex) {
            img = new ImageView();
        }
        img.setFitWidth(120);
        img.setFitHeight(150);
        img.setPreserveRatio(true);

        VBox info = new VBox(8);

        Label lTitulo = new Label(titulo);
        lTitulo.setStyle(
                "-fx-font-size: 20px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #1F3C88;"
        );

        Label lAnio = new Label("Año: " + anio);
        lAnio.setStyle("-fx-font-size: 14px;");

        Button btnVer = new Button("Ver revista");
        btnVer.setStyle(
                "-fx-background-color: #1F3C88;" +
                        "-fx-text-fill: white;" +
                        "-fx-padding: 8 20;" +
                        "-fx-background-radius: 6;" +
                        "-fx-font-size: 14px;"
        );

        // Hover button
        btnVer.setOnMouseEntered(e -> btnVer.setStyle(
                "-fx-background-color: #3B56A6;" +
                        "-fx-text-fill: white;" +
                        "-fx-padding: 8 20;" +
                        "-fx-background-radius: 6;" +
                        "-fx-font-size: 14px;"
        ));

        btnVer.setOnMouseExited(e -> btnVer.setStyle(
                "-fx-background-color: #1F3C88;" +
                        "-fx-text-fill: white;" +
                        "-fx-padding: 8 20;" +
                        "-fx-background-radius: 6;" +
                        "-fx-font-size: 14px;"
        ));

        if (rol.equalsIgnoreCase("INVITADO")) {
            btnVer.setDisable(true);
            btnVer.setText("Restringido");
            btnVer.setStyle(
                    "-fx-background-color: #cccccc;" +
                            "-fx-text-fill: white;" +
                            "-fx-padding: 8 20;" +
                            "-fx-background-radius: 6;"
            );
        } else {
            btnVer.setOnAction(e -> System.out.println("Ver revista: " + titulo));
        }

        info.getChildren().addAll(lTitulo, lAnio, btnVer);
        box.getChildren().addAll(img, info);
        card.getChildren().add(box);

        return card;
    }
}



package sgbu;

import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.ScrollPane;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class TesisDigitalesView {

    public void mostrar(Stage stage, String rol) {

        VBox container = new VBox(25);
        container.setPadding(new Insets(20));
        container.setAlignment(Pos.TOP_CENTER);
        container.setStyle("-fx-background-color: #F5F5F5;"); // Fondo gris claro igual a la imagen

        Label titulo = new Label("Tesis Digitales");
        titulo.setStyle(
                "-fx-font-size: 28px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #0B3D91;" + // Azul del título según la imagen
                        "-fx-padding: 10 0 20 0;"
        );
        container.getChildren().add(titulo);

        // Tesis
        container.getChildren().add(crearTesis("Implementación de algoritmos de IA", "Juan Pérez", "2023",
                "Ingeniería en Sistemas", "/img/tesis1.png", rol));

        container.getChildren().add(crearTesis("Sistema de recomendación con Deep Learning", "Luis A. Pérez", "2024",
                "Ingeniería en Sistemas", "/img/tesis2.png", rol));

        container.getChildren().add(crearTesis("Detección temprana de enfermedades oculares", "Ana M. Gutiérrez", "2023",
                "Ingeniería en Sistemas", "/img/tesis3.png", rol));

        container.getChildren().add(crearTesis("Competencias digitales en las MYPES de San Martín, 2021",
                "Br. Marx Kevin Erazo Panduro", "2021",
                "Ingeniería en Sistemas", "/img/tesis4.png", rol));

        container.getChildren().add(crearTesis("Tecnologías digitales aplicadas al escenario educativo",
                "Sanz Cecilia, Artola Verónica", "2022",
                "Ingeniería en Sistemas", "/img/tesis6.png", rol));

        container.getChildren().add(crearTesis("Sistema de información para la gestión documental",
                "Aranda-Manchay, Heyner Ronaldo", "2023",
                "Ingeniería en Sistemas", "/img/tesis8.png", rol));

        ScrollPane sp = new ScrollPane(container);
        sp.setFitToWidth(true);
        sp.setStyle("-fx-background: #F5F5F5;");

        Scene scene = new Scene(sp, 900, 650);
        stage.setScene(scene);
        stage.setTitle("Tesis Digitales");
        stage.show();
    }

    private HBox crearTesis(String titulo, String autor, String anio, String carrera, String imgRuta, String rol) {

        HBox h = new HBox(15);
        h.setPadding(new Insets(15));
        h.setAlignment(Pos.CENTER_LEFT);

        // Tarjeta igual a la imagen
        h.setStyle(
                "-fx-background-color: white;" +
                        "-fx-border-color: #3A5FCD;" + // Azul de borde igual a la imagen
                        "-fx-border-width: 1.7;" +
                        "-fx-border-radius: 12;" +
                        "-fx-background-radius: 12;" +
                        "-fx-effect: dropshadow(gaussian, rgba(0,0,0,0.08), 8, 0, 0, 2);"
        );

        ImageView img;
        try {
            img = new ImageView(new Image(getClass().getResource(imgRuta).toExternalForm()));
        } catch (Exception ex) {
            img = new ImageView();
        }

        img.setFitWidth(120);
        img.setFitHeight(140);

        VBox info = new VBox(6);

        Label lTitle = new Label(titulo);
        lTitle.setStyle(
                "-fx-font-size: 17px;" +
                        "-fx-font-weight: bold;" +
                        "-fx-text-fill: #0A0A0A;"
        );

        Label lAuthor = new Label("Autor: " + autor);
        lAuthor.setStyle("-fx-font-size: 14px; -fx-text-fill: #333;");

        Label lYear = new Label("Año: " + anio);
        lYear.setStyle("-fx-font-size: 14px; -fx-text-fill: #333;");

        Button btnDesc = new Button(rol.equalsIgnoreCase("INVITADO") ? "Restringido" : "Leer / Descargar");
        btnDesc.setStyle(
                "-fx-background-color: #1E63E9;" +  // Azul del botón según la imagen
                        "-fx-text-fill: white;" +
                        "-fx-font-weight: bold;" +
                        "-fx-background-radius: 8;" +
                        "-fx-padding: 8 18;"
        );

        if (rol.equalsIgnoreCase("INVITADO")) {
            btnDesc.setDisable(true);
            btnDesc.setStyle(
                    "-fx-background-color: #BFBFBF;" +
                            "-fx-text-fill: white;" +
                            "-fx-background-radius: 8;"
            );
        } else {
            btnDesc.setOnAction(e -> System.out.println("Descarga tesis: " + titulo));
        }

        info.getChildren().addAll(lTitle, lAuthor, lYear, btnDesc);
        h.getChildren().addAll(img, info);

        return h;
    }
}



// fxml



<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.Button?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.layout.VBox?>

<VBox spacing="18" alignment="CENTER"
      xmlns="http://javafx.com/javafx/17"
      xmlns:fx="http://javafx.com/fxml/1"
      fx:controller="sgbu.CONTROLLER.BibliotecarioViewController"
      style="-fx-background-color: #f4f4f4; -fx-padding: 40;">

    <Label text="📚 Panel del Bibliotecario" style="-fx-font-size: 25px;"/>

    <Button text="👥 Gestión de Usuarios" fx:id="btnUsuarios" prefWidth="250"/>
    <Button text="📚 Gestión de Recursos" fx:id="btnRecursos" prefWidth="250"/>
    <Button text="📋 Préstamos y Devoluciones" fx:id="btnPrestamos" prefWidth="250"/>
    <Button text="📊 Consultas / Reportes" fx:id="btnConsultas" prefWidth="250"/>

    <Button text="🔒 Cerrar Sesión"
            fx:id="btnCerrarSesion"
            style="-fx-background-color: #d9534f; -fx-text-fill: white;"
            prefWidth="250"/>

</VBox>






<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<BorderPane xmlns="http://javafx.com/javafx/17"
            xmlns:fx="http://javafx.com/fxml/1"
            style="-fx-background-color: #f5f5f5;">

    <top>
        <VBox style="-fx-background-color: #2c3e50; -fx-padding: 20;">
            <Label text="📋 Préstamos y Devoluciones"
                   style="-fx-font-size: 24px; -fx-font-weight: bold; -fx-text-fill: white;"/>
        </VBox>
    </top>

    <center>
        <VBox spacing="20" alignment="CENTER" style="-fx-padding: 40;">
            <Label text="Módulo de préstamos y devoluciones" style="-fx-font-size: 16px;"/>
            <Label text="(En desarrollo)" style="-fx-font-size: 14px; -fx-text-fill: #888;"/>
        </VBox>
    </center>

</BorderPane>



<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<BorderPane xmlns="http://javafx.com/javafx/17"
            xmlns:fx="http://javafx.com/fxml/1"
            style="-fx-background-color: #f5f5f5;">

    <top>
        <VBox style="-fx-background-color: #2c3e50; -fx-padding: 20;">
            <Label text="📚 Gestión de Recursos"
                   style="-fx-font-size: 24px; -fx-font-weight: bold; -fx-text-fill: white;"/>
        </VBox>
    </top>

    <center>
        <VBox spacing="20" alignment="CENTER" style="-fx-padding: 40;">
            <Label text="Módulo de gestión de recursos" style="-fx-font-size: 16px;"/>
            <Label text="(En desarrollo)" style="-fx-font-size: 14px; -fx-text-fill: #888;"/>
        </VBox>
    </center>

</BorderPane>


<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.Insets?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<BorderPane xmlns="http://javafx.com/javafx/17"
            xmlns:fx="http://javafx.com/fxml/1"
            style="-fx-background-color: #f5f5f5;">

    <top>
        <VBox style="-fx-background-color: #2c3e50; -fx-padding: 20;">
            <Label text="👥 Gestión de Usuarios"
                   style="-fx-font-size: 24px; -fx-font-weight: bold; -fx-text-fill: white;"/>
        </VBox>
    </top>

    <center>
        <VBox spacing="20" alignment="CENTER" style="-fx-padding: 40;">
            <Label text="Módulo de gestión de usuarios" style="-fx-font-size: 16px;"/>
            <Label text="(En desarrollo)" style="-fx-font-size: 14px; -fx-text-fill: #888;"/>
        </VBox>
    </center>

</BorderPane> 



<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<AnchorPane xmlns="http://javafx.com/javafx"
            xmlns:fx="http://javafx.com/fxml"
            fx:controller="sgbu.CONTROLLER.GestionPrestamosController">

    <children>
        <TableView fx:id="tablaPrestamos" layoutX="20" layoutY="20" prefWidth="760" prefHeight="400">
            <columns>

                <TableColumn fx:id="colRecurso" text="Recurso" prefWidth="160"/>
                <TableColumn fx:id="colTipo" text="Tipo" prefWidth="120"/>
                <TableColumn fx:id="colFechaPrestamo" text="Fecha Préstamo" prefWidth="140"/>
                <TableColumn fx:id="colFechaLimite" text="Fecha Límite" prefWidth="140"/>
                <TableColumn fx:id="colEstado" text="Estado" prefWidth="100"/>
                <TableColumn fx:id="colAccion" text="Acción" prefWidth="100"/>

            </columns>
        </TableView>
    </children>
</AnchorPane>


