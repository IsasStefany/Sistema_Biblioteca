package sgbu;

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





