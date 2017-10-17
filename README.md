### cpu.jsp
```
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<%@ page import="org.jfree.chart.servlet.ServletUtilities,bean.cpu" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'cpu.jsp' starting page</title>
    
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
     <%
    	//String fileName=ServletUtilities.saveChartAsJPEG(cpu.createChart(),500,300,session);
    	String fileName=null;
        fileName=ServletUtilities.saveChartAsJPEG(cpu.createChart(),800,500,session);
    	String graphURL=request.getContextPath()+"/servlet/DisplayChart?filename="+fileName;
     %>
     
     <div align="center">
     	<img src="<%=graphURL%>" border="1"><br><br>
     	<a href="servlet/_zhu">交换区</a><br>
    	<a href="servlet/_neicun">内存</a><br>
     	<a href="quyu.jsp">返回</a>
     </div><br>
  </body>
</html>

```
### _cpu.jsp
```
package bean;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.jfree.chart.servlet.ServletUtilities;

public class _cpu extends HttpServlet {
	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		String fileName=ServletUtilities.saveChartAsJPEG(
				cpu.createChart(),500,300,request.getSession()
				);
		String graphURL=request.getContextPath()+"/servlet/DisplayChart?filename="+fileName;
		request.setAttribute("graphURL",graphURL);
		request.getRequestDispatcher("/cpu.jsp").forward(request,response);
		
	}

}

```
### cpu.jsp
```
package bean;

import java.awt.Font;
import java.awt.Image;
import java.io.IOException;
import java.text.DecimalFormat;
import java.text.NumberFormat;

import javax.imageio.ImageIO;

import org.hyperic.sigar.CpuPerc;
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
import org.jfree.chart.labels.StandardPieSectionLabelGenerator;
import org.jfree.chart.plot.CategoryPlot;
import org.jfree.chart.plot.PiePlot;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.chart.renderer.category.CategoryItemRenderer;
import org.jfree.chart.title.TextTitle;
import org.jfree.data.category.CategoryDataset;
import org.jfree.data.category.DefaultCategoryDataset;
import org.jfree.ui.VerticalAlignment;

import com.orsoncharts.renderer.category.BarRenderer3D;

public class cpu {
	public static CategoryDataset createDataSet(){
		DefaultCategoryDataset dataset=new DefaultCategoryDataset();
		Sigar sigar = new Sigar();      //获取Sigar对象
		CpuPerc cpuList[] = null;
		 try {  
	            cpuList = sigar.getCpuPercList();      //获取CPU对象
	        } catch (SigarException e) {  
	            e.printStackTrace();  
	            return null;  
	        }  
	        for (int i = 0; i < cpuList.length; i++) {  
	        	dataset.addValue(cpuList[i].getUser(),"用户使用率","第"+i+"块cpu");   //使用CPU的几个方法获取各种信息
	        	dataset.addValue(cpuList[i].getSys(),"系统使用率","第"+i+"块cpu");     //并且加入数据集合中
	        	dataset.addValue(cpuList[i].getWait(),"当前等待率","第"+i+"块cpu");
	        	dataset.addValue(cpuList[i].getIdle(),"当前空闲率","第"+i+"块cpu");
	        	dataset.addValue(cpuList[i].getCombined(),"总使用率","第"+i+"块cpu");
	        }  
		return dataset;
	}

	public static JFreeChart createChart() {  
        //创建3D饼形图表  
        JFreeChart chart = ChartFactory.createBarChart3D(  
                "本机CPU信息",  
                "cpu",		//图表标题  
                "使用率",
                createDataSet(),  
        //饼形图的数据集对象
                PlotOrientation.VERTICAL,
                true,  
                //是否显示图例  
                true,  
                //是否显示提示文本  
                false); 
        StandardChartTheme standardChartTheme = new StandardChartTheme("CN");       
        // 设置标题字体       
        standardChartTheme.setExtraLargeFont(new Font("宋书", Font.BOLD, 20));       
        // 设置图例的字体       
        standardChartTheme.setRegularFont(new Font("宋书", Font.PLAIN, 15));       
        // 设置轴向的字体       
        standardChartTheme.setLargeFont(new Font("宋书", Font.PLAIN, 15));       
        // 应用主题样式       
        ChartFactory.setChartTheme(standardChartTheme);   
     
        return chart;  
    }  
}

```
