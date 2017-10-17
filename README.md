### neicun.jsp
```
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'zhu.jsp' starting page</title>
    
	<meta http-equiv="pragma" content="no-cache">
	<meta http-equiv="cache-control" content="no-cache">
	<meta http-equiv="expires" content="0">    
	<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
	<meta http-equiv="description" content="This is my page">
	<!--
	<link rel="stylesheet" type="text/css" href="styles.css">
	-->

  </head>
  
  <body>
    	<div align="center">
    		<img src="${graphURL }" border="1">
    		<br><br>
    		<a href="servlet/_zhu">交换区</a><br>
    	<a href="servlet/_cpu">cpu</a><br>
    		<a href="quyu.jsp">返回</a>
    	</div>
  </body>
</html>

```
### _neicun.java
```
package bean;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.jfree.chart.servlet.ServletUtilities;

public class _neicun extends HttpServlet {

	/**
	 * The doGet method of the servlet. <br>
	 *
	 * This method is called when a form has its tag value method equals to get.
	 * 
	 * @param request the request send by the client to the server
	 * @param response the response send by the server to the client
	 * @throws ServletException if an error occurred
	 * @throws IOException if an error occurred
	 */
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		String fileName=ServletUtilities.saveChartAsJPEG(
				neicun.createChart(),500,300,request.getSession()
				);
		String graphURL=request.getContextPath()+"/servlet/DisplayChart?filename="+fileName;
		request.setAttribute("graphURL",graphURL);
		request.getRequestDispatcher("/neicun.jsp").forward(request,response);
		
	}

}

```
### neicun.java
```
package bean;

import java.awt.Font;
import java.awt.Image;
import java.io.IOException;
import java.text.DecimalFormat;

import javax.imageio.ImageIO;

import org.hyperic.sigar.FileSystem;
import org.hyperic.sigar.FileSystemUsage;
import org.hyperic.sigar.Mem;
import org.hyperic.sigar.Sigar;
import org.hyperic.sigar.SigarException;
import org.hyperic.sigar.Swap;
import org.jfree.chart.ChartFactory;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.StandardChartTheme;
import org.jfree.chart.axis.CategoryAxis;
import org.jfree.chart.axis.CategoryLabelPositions;
import org.jfree.chart.axis.ValueAxis;
import org.jfree.chart.plot.CategoryPlot;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.chart.title.TextTitle;
import org.jfree.data.category.CategoryDataset;
import org.jfree.data.category.DefaultCategoryDataset;
import org.jfree.ui.VerticalAlignment;

import com.orsoncharts.renderer.category.BarRenderer3D;

public class neicun {
	public static CategoryDataset createDataSet(){
		DefaultCategoryDataset dataset=new DefaultCategoryDataset();
		DecimalFormat df=new DecimalFormat("#0.00");
		Sigar sigar=new Sigar();
		Mem mem;
		try{
			//内存
			mem=sigar.getMem();
			dataset.addValue(mem.getTotal() / 1024/1024,"内存","总量");
			dataset.addValue(mem.getUsed()/1024/1024,"内存","使用量");
			dataset.addValue(mem.getFree()/1024/1024,"内存","剩余量");
			System.out.println("内存总量："+df.format((float)mem.getTotal() / 1024/1024/1024) + "G");
		}catch(SigarException e){
			e.printStackTrace();
		}
		
		return dataset;
	}
	public static JFreeChart createChart(){
		
		StandardChartTheme stand=new StandardChartTheme("CN");
		stand.setExtraLargeFont(new Font("隶书",Font.BOLD,20));
		stand.setRegularFont(new Font("宋体",Font.PLAIN,15));
		stand.setLargeFont(new Font("宋体",Font.PLAIN,15));
		ChartFactory.setChartTheme(stand);    
		JFreeChart ch=ChartFactory.createBarChart3D(
				"电脑内存信息",
				"内存",
				"量(M)",
				createDataSet(),
				PlotOrientation.VERTICAL,
				true,
				false,
				false
				);
		return ch;
		}
	
}

```
