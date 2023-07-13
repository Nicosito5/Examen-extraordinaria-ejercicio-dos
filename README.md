# Examen-extraordianria-ejercicio-dos

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

// Clase principal que representa la plataforma de la librería
public class LibreriaOnline {
    private List<Usuario> usuarios;
    private List<Producto> productos;
    private List<Venta> ventas;

    public LibreriaOnline() {
        usuarios = new ArrayList<>();
        productos = new ArrayList<>();
        ventas = new ArrayList<>();
    }

    // Métodos para dar de alta/baja un producto
    public void agregarProducto(Producto producto) {
        productos.add(producto);
    }

    public void eliminarProducto(int idProducto) {
        productos.removeIf(p -> p.getId() == idProducto);
    }

    // Métodos para dar de alta/baja un usuario
    public void agregarUsuario(Usuario usuario) {
        usuarios.add(usuario);
    }

    public void eliminarUsuario(String dni) {
        usuarios.removeIf(u -> u.getDni().equals(dni));
    }

    // Método para comprar un producto
    public void comprarProducto(int idProducto, String dniUsuario) throws Exception {
        Usuario usuario = buscarUsuario(dniUsuario);
        Producto producto = buscarProducto(idProducto);

        if (usuario != null && producto != null) {
            if (usuario.getEdad() >= 10 && usuario.getEdad() <= 18) {
                Usuario padre = buscarUsuario(usuario.getDniPadre());
                if (padre == null) {
                    throw new Exception("Se requiere la autorización del padre.");
                }
            }

            Venta venta = new Venta(usuario, producto);
            ventas.add(venta);
        } else {
            throw new Exception("El usuario o el producto no existen.");
        }
    }

    // Método para devolver un producto
    public void devolverProducto(int idProducto, String dniUsuario) throws Exception {
        Usuario usuario = buscarUsuario(dniUsuario);
        Producto producto = buscarProducto(idProducto);

        if (usuario != null && producto != null) {
            Venta venta = buscarVenta(usuario, producto);
            if (venta != null && venta.isReciente()) {
                ventas.remove(venta);
            } else {
                throw new Exception("No es posible devolver este producto.");
            }
        } else {
            throw new Exception("El usuario o el producto no existen.");
        }
    }

    // Método para listar productos por título y vendidos, diferenciando entre libros y juegos de mesa
    public List<Producto> listarProductosPorTitulo() {
        productos.sort((p1, p2) -> p1.getTitulo().compareToIgnoreCase(p2.getTitulo()));
        return productos;
    }

    // Método para obtener el dinero ingresado procedente de las ventas de libros en un determinado mes/año
    public double dineroIngresadoLibros(int mes, int año) {
        double totalIngresos = 0;

        for (Venta venta : ventas) {
            if (venta.getProducto() instanceof Libro) {
                if (venta.getFecha().getMonthValue() == mes && venta.getFecha().getYear() == año) {
                    totalIngresos += venta.getProducto().getPrecio();
                }
            }
        }

        return totalIngresos;
    }

    // Método para obtener el dinero ingresado procedente de las ventas de juegos en un determinado mes/año
    public double dineroIngresadoJuegos(int mes, int año) {
        double totalIngresos = 0;

        for (Venta venta : ventas) {
            if (venta.getProducto() instanceof JuegoMesa) {
                if (venta.getFecha().getMonthValue() == mes && venta.getFecha().getYear() == año) {
                    totalIngresos += venta.getProducto().getPrecio();
                }
            }
        }

        return totalIngresos;
    }

    // Método para obtener la cantidad de libros vendidos en un determinado mes
    public int cantidadLibrosVendidos(int mes) {
        int totalLibrosVendidos = 0;

        for (Venta venta : ventas) {
            if (venta.getProducto() instanceof Libro) {
                if (venta.getFecha().getMonthValue() == mes) {
                    totalLibrosVendidos++;
                }
            }
        }

        return totalLibrosVendidos;
    }

    // Método para obtener la cantidad de juegos de mesa vendidos en un determinado mes
    public int cantidadJuegosVendidos(int mes) {
        int totalJuegosVendidos = 0;

        for (Venta venta : ventas) {
            if (venta.getProducto() instanceof JuegoMesa) {
                if (venta.getFecha().getMonthValue() == mes) {
                    totalJuegosVendidos++;
                }
            }
        }

        return totalJuegosVendidos;
    }

    // Método para obtener el ranking por título de libros vendidos en un determinado mes
    public List<String> rankingLibrosVendidos(int mes) {
        Map<String, Integer> contadorLibros = new HashMap<>();

        for (Venta venta : ventas) {
            if (venta.getProducto() instanceof Libro) {
                if (venta.getFecha().getMonthValue() == mes) {
                    Libro libro = (Libro) venta.getProducto();
                    int contador = contadorLibros.getOrDefault(libro.getTitulo(), 0);
                    contadorLibros.put(libro.getTitulo(), contador + 1);
                }
            }
        }

        List<String> rankingLibros = new ArrayList<>();

        for (Map.Entry<String, Integer> entry : contadorLibros.entrySet()) {
            rankingLibros.add(entry.getKey() + " (" + entry.getValue() + " vendidos)");
        }

        rankingLibros.sort((s1, s2) -> {
            int cantidad1 = Integer.parseInt(s1.substring(s1.lastIndexOf("(") + 1, s1.lastIndexOf(")")).trim());
            int cantidad2 = Integer.parseInt(s2.substring(s2.lastIndexOf("(") + 1, s2.lastIndexOf(")")).trim());
            return cantidad2 - cantidad1;
        });

        return rankingLibros;
    }

    // Método para obtener el ranking por título de juegos de mesa vendidos en un determinado mes
    public List<String> rankingJuegosVendidos(int mes) {
        Map<String, Integer> contadorJuegos = new HashMap<>();

        for (Venta venta : ventas) {
            if (venta.getProducto() instanceof JuegoMesa) {
                if (venta.getFecha().getMonthValue() == mes) {
                    JuegoMesa juego = (JuegoMesa) venta.getProducto();
                    int contador = contadorJuegos.getOrDefault(juego.getTitulo(), 0);
                    contadorJuegos.put(juego.getTitulo(), contador + 1);
                }
            }
        }

        List<String> rankingJuegos = new ArrayList<>();

        for (Map.Entry<String, Integer> entry : contadorJuegos.entrySet()) {
            rankingJuegos.add(entry.getKey
