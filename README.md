## Here you have the link of the video. PART 1, Link: https://www.youtube.com/watch?v=HUkA3rfzT6c

public void exportarAPDF(JTable tabla) {
        try {
            // Ruta donde se guardarán los archivos PDF
            String rutaPDF = "C:\\Users\\accpm\\OneDrive\\Escritorio\\ARCHIVOS_EXPORTADOS\\ARCHIVOS_PDF\\PRODUCTOS_PDF";

            // Verificar si la carpeta existe, de lo contrario, crearla
            File carpetaPDF = new File(rutaPDF);
            if (!carpetaPDF.exists()) {
                carpetaPDF.mkdirs();
            }

            // Obtener el número de reporte actual
            int numeroReporte = obtenerNumeroReporte();
            incrementarNumeroReporte(); // Incrementar el número de reporte para el siguiente

            // Nombre del archivo PDF con fecha y número de reporte
            String nombreArchivo = generarNombreArchivoReporte(numeroReporte);
            String filePath = rutaPDF + File.separator + nombreArchivo;

            // Crear el documento PDF
            Document documento = new Document(PageSize.A4);
            PdfWriter.getInstance(documento, new FileOutputStream(filePath));
            documento.open();

            // Crear una tabla iText para el encabezado
            PdfPTable encabezadoTable = new PdfPTable(2);
            encabezadoTable.setWidthPercentage(100);

            // Celda izquierda: Agregar imagen
            PdfPCell celdaImagen = new PdfPCell();
            celdaImagen.setBorder(PdfPCell.NO_BORDER);

            Image imagen = Image.getInstance("C:\\Users\\accpm\\OneDrive\\Documentos\\NetBeansProjects\\Proyecto2_Inventario\\src\\main\\resources\\com\\imagenes_orginal\\lggoo.png");
            imagen.scaleToFit(80, 80);
            celdaImagen.addElement(imagen);

            encabezadoTable.addCell(celdaImagen);

            // Celda derecha: Agregar texto y fecha
            PdfPCell celdaTexto = new PdfPCell();
            celdaTexto.setBorder(PdfPCell.NO_BORDER);

            Paragraph titulo = new Paragraph("Gestión De Productos", FontFactory.getFont(FontFactory.HELVETICA_BOLD, 18));
            titulo.setAlignment(Element.ALIGN_CENTER);

            SimpleDateFormat dateFormat = new SimpleDateFormat("dd/MM/yyyy");
            String fecha = "Fecha: " + dateFormat.format(new Date());

            Paragraph fechaActual = new Paragraph(fecha, FontFactory.getFont(FontFactory.HELVETICA, 12));
            fechaActual.setAlignment(Element.ALIGN_RIGHT);

            celdaTexto.addElement(titulo);
            celdaTexto.addElement(fechaActual);

            encabezadoTable.addCell(celdaTexto);

            // Agregar encabezado a la página
            documento.add(encabezadoTable);

            // Espaciado después del encabezado
            documento.add(new Paragraph(" "));

            // Agregar el título del reporte
            Paragraph tituloReporte = new Paragraph("Reporte de Productos en Stock", FontFactory.getFont(FontFactory.HELVETICA_BOLD, 14));
            tituloReporte.setAlignment(Element.ALIGN_CENTER);
            documento.add(tituloReporte);

            // Agregar espacio en blanco
            documento.add(new Paragraph(" "));

            // Agregar datos de la JTable a la tabla iText
            PdfPTable pdfTable = new PdfPTable(tabla.getColumnCount());
            pdfTable.setWidthPercentage(100);

            // Obtener encabezados de columna de la tabla y agregarlos con fondo gris
            JTableHeader header = tabla.getTableHeader();
            for (int i = 0; i < tabla.getColumnCount(); i++) {
                PdfPCell cell = new PdfPCell(new Phrase(header.getColumnModel().getColumn(i).getHeaderValue().toString(), FontFactory.getFont(FontFactory.HELVETICA_BOLD, 12)));
                cell.setHorizontalAlignment(Element.ALIGN_CENTER);
                cell.setBackgroundColor(BaseColor.LIGHT_GRAY); // Color de fondo gris
                pdfTable.addCell(cell);
            }

            // Agregar datos de la JTable a la tabla iText
            for (int i = 0; i < tabla.getRowCount(); i++) {
                for (int j = 0; j < tabla.getColumnCount(); j++) {
                    PdfPCell cell = new PdfPCell(new Phrase(tabla.getValueAt(i, j).toString(), FontFactory.getFont(FontFactory.HELVETICA, 12)));
                    cell.setHorizontalAlignment(Element.ALIGN_CENTER);
                    pdfTable.addCell(cell);
                }
            }

            documento.add(pdfTable);

            documento.close();

            // Abre el archivo PDF una vez que se ha creado
            if (Desktop.isDesktopSupported()) {
                Desktop desktop = Desktop.getDesktop();
                File archivoPDF = new File(filePath);
                if (archivoPDF.exists()) {
                    desktop.open(archivoPDF);
                } else {
                    Toolkit.getDefaultToolkit().beep();
                    JOptionPane.showMessageDialog(null, "El archivo PDF no existe.", "ERROR", JOptionPane.ERROR_MESSAGE);
                }
            }

            // Mostrar un mensaje de éxito
            Toolkit.getDefaultToolkit().beep();
            JOptionPane.showMessageDialog(null, "Datos exportados correctamente a un archivo PDF en " + filePath);
        } catch (IOException | DocumentException e) {
            e.printStackTrace();
            Toolkit.getDefaultToolkit().beep();
            JOptionPane.showMessageDialog(null, "Error al exportar datos a PDF: " + e.getMessage());
        }
    }
