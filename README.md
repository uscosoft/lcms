lcms
====

LCMS
package org.uscosoft.curso;

import java.io.IOException;
import java.io.PrintWriter;
import java.net.URL;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.uscosoft.utilidad.BaseDato;

/**
 * Servlet implementation class curso
 */
public class Curso extends HttpServlet {
  private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public Curso() {
        super();
        // TODO Auto-generated constructor stub
    }

    
	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doPost(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		
		PrintWriter out = response.getWriter();
		
		String metodo = request.getParameter("metodo");
		
		if(metodo.equals("getCursos")){
			response.setContentType("text/xml");
			String respuesta = getCursos(request);
			out.println(respuesta);
		}
		
		System.out.println(metodo);
		
		if(metodo.equals("getAsignatura")){
			response.setContentType("text/xml");
			String respuesta = getAsignatura(request);
			out.println(respuesta);
		}
		
		if(metodo.equals("getDocente")){
			response.setContentType("text/xml");
			String respuesta = getDocente(request);
			out.println(respuesta);
		}
		
		if(metodo.equals("getCalendario")){
			response.setContentType("text/xml");
			String respuesta = getCalendario(request);
			out.println(respuesta);
		}
		
		if(metodo.equals("editar")){
			response.setContentType("text/xml");
			String respuesta = editar(request);
			out.println(respuesta);
		}
		
		System.out.println("final");
	}

	public String editar(HttpServletRequest request){
		String respuesta = "";
		
		BaseDato basedato = new BaseDato();
		
		Connection conexion1 = null;
		Statement sentencia1 = null;
		ResultSet rs1 = null;
		
		String sql = "";
		
		try{
			conexion1 = basedato.getConexion("ADMINISTRADOR_SISTEMA");
			sentencia1 = conexion1.createStatement();
			
			String accion = request.getParameter("accion");
			String codigo = request.getParameter("codigo");
			
			String nombre = request.getParameter("nombre");
			String asignatura = request.getParameter("asignatura");
			String calendario = request.getParameter("calendario");
			String docente = request.getParameter("docente");
		    String descripcion = request.getParameter("descripcion");
			String usuario = "0";
			
			String login = request.getUserPrincipal().getName();
			
			sql = "select usu_codigo from usuario where usu_login = '" + login + "'";
			
			System.out.println(sql);
			
			rs1 = sentencia1.executeQuery(sql);
			
			if(rs1.next()){
				usuario = rs1.getString("usu_codigo");
			}
					
			if(accion.toUpperCase().equals("ADICIONAR")){
				sql = "INSERT INTO curso(cur_nombre, cur_asignatura, cur_calendario, cur_descripcion, cur_docente)";
			    sql = sql + "VALUES ('" + nombre + "'," + asignatura + "," + calendario + ",'" + descripcion + "'," +  docente + ")";
			}
			if(accion.toUpperCase().equals("MODIFICAR")){
				sql = "UPDATE curso";
				sql = sql + " SET cur_nombre = '" + nombre + "'";
				sql = sql + " , cur_asignatura = " + asignatura ;
				sql = sql + " , cur_calendario = " + calendario ;
				sql = sql + " , cur_descripcion = '" + descripcion + "'"; 
				sql = sql + " , cur_docente = " + docente ; 
				sql = sql + " WHERE cur_codigo = " + codigo;
		
			}
			if(accion.toUpperCase().equals("ELIMINAR")){
				sql = "DELETE FROM curso";
				sql = sql + " WHERE cur_codigo = " + codigo;
			}

			System.out.println(sql);
			
			
			int actualizados = sentencia1.executeUpdate(sql);
			
			if(actualizados > 0){
				respuesta = "<respuesta exito='true'>";
				respuesta = respuesta + "<codigo>48</codigo>";
				respuesta = respuesta + "<mensaje>Operaci�n ejecutada exitosamente.</mensaje>";
				respuesta = respuesta + "</respuesta>";
			}
			else{
				//respuesta = "<respuesta>";
				//respuesta = respuesta + "<codigo>16</codigo>";
				//respuesta = respuesta + "<mensaje>Error al ejecutar la operaci�n.</mensaje>";
				//respuesta = respuesta + "</respuesta>";
				
				respuesta = "<respuesta exito='false'>";
				respuesta = respuesta + "<errors>";
				respuesta = respuesta + "<field>";
				respuesta = respuesta + "<id>nombre</id>";
				respuesta = respuesta + "<msg> Nombre no valido. <br /><i>Por favor digite el nombre del curso </i> </msg>";
				respuesta = respuesta + "</field>";
				respuesta = respuesta + "</errors>";
				respuesta = respuesta + "</respuesta>";
				
			}
				
		}
		catch(Exception e){
			System.out.println("Error: " + e.toString());
		}
		finally{
			if(rs1 != null){
				try {
					rs1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			rs1 = null;
			
			if(sentencia1 != null){
				try {
					sentencia1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			sentencia1 = null;
			
			if(conexion1 != null){
				try {
					conexion1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			conexion1 = null;
		}
		
		
		return respuesta;
	}
	
	
	public String getCursos(HttpServletRequest request){
		String respuesta = "";
		
		BaseDato basedato = new BaseDato();
		
		Connection conexion1 = null;
		Statement sentencia1 = null;
		ResultSet rs1 = null;
		
		String sql = "";
		try{
			conexion1 = basedato.getConexion("ADMINISTRADOR_SISTEMA");
			sentencia1 = conexion1.createStatement();
			
			String codigo = request.getParameter("codigo");
			
			String limit = request.getParameter("limit");
			String start = request.getParameter("start");
			
			if(codigo == null){
				codigo = "0";
			}
			if(limit == null){
				limit = "10";
			}
			if(start == null){
				start = "0";
			}
			
			int total_registros = 0;
			sql = "select count(*) as total from curso";
			rs1 = sentencia1.executeQuery(sql);
			if(rs1.next()){
				total_registros = rs1.getInt("total");
			}
			
			sql = "select * from curso";
			
			if(!codigo.equals("0")){
				sql = sql + " where cur_codigo = " + codigo;
			}
			
			sql = sql + " offset " + start;
			sql = sql + " limit " + limit;
			rs1 = sentencia1.executeQuery(sql);
			
			respuesta = "<datos>";
			respuesta = respuesta + "<info>";
			respuesta = respuesta + "<total>" + total_registros + "</total>";
			respuesta = respuesta + "</info>";
			
			respuesta = respuesta + "<registros>";
			
			while(rs1.next()){
				codigo = rs1.getString("cur_codigo");
				String asignatura = rs1.getString("cur_asignatura");
				String calendario = rs1.getString("cur_calendario");
				String docente = rs1.getString("cur_docente");
				String nombre = rs1.getString("cur_nombre");
				String descripcion = rs1.getString("cur_descripcion");
				
				respuesta = respuesta + "<registro>";
				respuesta = respuesta + "<codigo>" + codigo + "</codigo>";
				respuesta = respuesta + "<asignatura>" + asignatura + "</asignatura>";
				respuesta = respuesta + "<calendario>" + calendario + "</calendario>";
				respuesta = respuesta + "<docente>" + docente + "</docente>";
				respuesta = respuesta + "<nombre>" + nombre + "</nombre>";
				respuesta = respuesta + "<descripcion>" + descripcion + "</descripcion>";
				respuesta = respuesta + "</registro>";
			}
			
			respuesta = respuesta + "</registros>";
			respuesta = respuesta + "</datos>";
			
		}
		catch(Exception e){
			System.out.println("Error: " + e.toString());
		}
		finally{
			if(rs1 != null){
				try {
					rs1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			rs1 = null;
			
			if(sentencia1 != null){
				try {
					sentencia1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			sentencia1 = null;
			
			if(conexion1 != null){
				try {
					conexion1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			conexion1 = null;
		}
		
		
		return respuesta;
	}
	
	
	public String getDocente(HttpServletRequest request){
		String respuesta = "";
		
		BaseDato basedato = new BaseDato();
		
		Connection conexion1 = null;
		Statement sentencia1 = null;
		ResultSet rs1 = null;
		
		String sql = "";
		try{
			System.out.println("1");
			conexion1 = basedato.getConexion("ADMINISTRADOR_SISTEMA");
			sentencia1 = conexion1.createStatement();
			
			System.out.println("2");
			
			sql = "select * from docente";
			rs1 = sentencia1.executeQuery(sql);
			
			System.out.println("3");
			
			respuesta = "<registros>";
			
			while(rs1.next()){
				String codigo = rs1.getString("doc_codigo");
				String nombre = rs1.getString("doc_nombre");
				//String apellido = rs1.getString("per_apellido");
				System.out.println(codigo + "," + nombre  );
				
				respuesta = respuesta + "<registro>";
				respuesta = respuesta + "<codigo>" + codigo + "</codigo>";
				respuesta = respuesta + "<nombre>" + nombre + "</nombre>";
				//respuesta = respuesta + "<apellido>" + apellido + "</apellido>";
				respuesta = respuesta + "</registro>";
			}
			
			respuesta = respuesta + "</registros>";
			
		}
		catch(Exception e){
			System.out.println("Error: " + e.toString());
		}
		finally{
			if(rs1 != null){
				try {
					rs1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			rs1 = null;
			
			if(sentencia1 != null){
				try {
					sentencia1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			sentencia1 = null;
			
			if(conexion1 != null){
				try {
					conexion1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			conexion1 = null;
		}
		
		return respuesta;
	}
	
	public String getCalendario(HttpServletRequest request){
		String respuesta = "";
		
		BaseDato basedato = new BaseDato();
		
		Connection conexion1 = null;
		Statement sentencia1 = null;
		ResultSet rs1 = null;
		
		String sql = "";
		try{
			System.out.println("1");
			conexion1 = basedato.getConexion("ADMINISTRADOR_SISTEMA");
			sentencia1 = conexion1.createStatement();
			
			System.out.println("2");
			
			sql = "select * from calendario";
			rs1 = sentencia1.executeQuery(sql);
			
			System.out.println("3");
			
			respuesta = "<registros>";
			
			while(rs1.next()){
				String codigo = rs1.getString("cal_codigo");
				String nombre = rs1.getString("cal_nombre");
				//String recurso = rs1.getString("cre_recurso");
				System.out.println(codigo + "," + nombre);
				
				respuesta = respuesta + "<registro>";
				respuesta = respuesta + "<codigo>" + codigo + "</codigo>";
				respuesta = respuesta + "<nombre>" + nombre + "</nombre>";
				//respuesta = respuesta + "<recurso>" + recurso + "</recurso>";
				respuesta = respuesta + "</registro>";
			}
			
			respuesta = respuesta + "</registros>";
			
		}
		catch(Exception e){
			System.out.println("Error: " + e.toString());
		}
		finally{
			if(rs1 != null){
				try {
					rs1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			rs1 = null;
			
			if(sentencia1 != null){
				try {
					sentencia1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			sentencia1 = null;
			
			if(conexion1 != null){
				try {
					conexion1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			conexion1 = null;
		}
		
		return respuesta;
	}
	
	public String getAsignatura(HttpServletRequest request){
		String respuesta = "";
		
		BaseDato basedato = new BaseDato();
		
		Connection conexion1 = null;
		Statement sentencia1 = null;
		ResultSet rs1 = null;
		
		String sql = "";
		try{
			System.out.println("1");
			conexion1 = basedato.getConexion("ADMINISTRADOR_SISTEMA");
			sentencia1 = conexion1.createStatement();
			
			System.out.println("2");
			
			sql = "select * from asignatura";
			rs1 = sentencia1.executeQuery(sql);
			
			System.out.println("3");
			
			respuesta = "<registros>";
			
			while(rs1.next()){
				String codigo = rs1.getString("asi_codigo");
				String nombre = rs1.getString("asi_nombre");
				//String recurso = rs1.getString("cre_recurso");
				System.out.println(codigo + "," + nombre);
				
				respuesta = respuesta + "<registro>";
				respuesta = respuesta + "<codigo>" + codigo + "</codigo>";
				respuesta = respuesta + "<nombre>" + nombre + "</nombre>";
				//respuesta = respuesta + "<recurso>" + recurso + "</recurso>";
				respuesta = respuesta + "</registro>";
			}
			
			respuesta = respuesta + "</registros>";
			
		}
		catch(Exception e){
			System.out.println("Error: " + e.toString());
		}
		finally{
			if(rs1 != null){
				try {
					rs1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			rs1 = null;
			
			if(sentencia1 != null){
				try {
					sentencia1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			sentencia1 = null;
			
			if(conexion1 != null){
				try {
					conexion1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			conexion1 = null;
		}
		
		return respuesta;
	}
	
	public ArrayList getCursos(){
		ArrayList listaCursos = new ArrayList() ; 
		
		BaseDato basedato = new BaseDato();
		
		Connection conexion1 = null;
		Statement sentencia1 = null;
		ResultSet rs1 = null;
		
		String sql = "";
		try{
			System.out.println("1");
			conexion1 = basedato.getConexion("ADMINISTRADOR_SISTEMA");
			sentencia1 = conexion1.createStatement();
			
			sql = "select * from curso";
			rs1 = sentencia1.executeQuery(sql);
			
			while(rs1.next()){
				String codigo = rs1.getString("cur_codigo");
				String nombre = rs1.getString("cur_nombre");
				String asignatura = rs1.getString("cur_asignatura");
				String calendario = rs1.getString("cur_calendario");
				String docente = rs1.getString("cur_docente");
				String descripcion = rs1.getString("cur_descripcion");
				
				String [] datos = new String[7];
				datos[0] = codigo;
				datos[1] = nombre;
				datos[2] = asignatura;
				datos[3] = calendario;
				datos[4] = docente;
				datos[5] = descripcion;
				
				listaCursos.add(datos); 
				
			}
		}
		catch(Exception e){
			System.out.println("Error: " + e.toString());
		}
		finally{
			if(rs1 != null){
				try {
					rs1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			rs1 = null;
			
			if(sentencia1 != null){
				try {
					sentencia1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			sentencia1 = null;
			
			if(conexion1 != null){
				try {
					conexion1.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			conexion1 = null;
		}
		
		return listaCursos;
	}
	
}
