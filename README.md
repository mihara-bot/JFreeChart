### zhu.jsp
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
    	<a href="servlet/_neicun">内存</a><br>
    	<a href="servlet/_cpu">cpu</a><br>
    		<a href="quyu.jsp">返回</a>
    	</div>
  </body>
</html>

```
### _zhu.java
```
package bean;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.jfree.chart.servlet.ServletUtilities;

public class _zhu extends HttpServlet {

	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		String fileName=ServletUtilities.saveChartAsJPEG(
				zhu.createChart(),500,300,request.getSession()
				);
		String graphURL=request.getContextPath()+"/servlet/DisplayChart?filename="+fileName;
		request.setAttribute("graphURL",graphURL);
		request.getRequestDispatcher("/zhu.jsp").forward(request,response);
		
	}

}

```
### zhu.jsp
```
package bean;

import java.awt.Color;
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
import org.jfree.chart.labels.StandardPieSectionLabelGenerator;
import org.jfree.chart.plot.CategoryPlot;
import org.jfree.chart.plot.PiePlot;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.chart.renderer.category.CategoryItemRenderer;
import org.jfree.chart.title.TextTitle;
import org.jfree.data.category.CategoryDataset;
import org.jfree.data.category.DefaultCategoryDataset;
import org.jfree.ui.VerticalAlignment;

import com.orsoncharts.label.StandardCategoryItemLabelGenerator;
import com.orsoncharts.renderer.category.BarRenderer3D;

public class zhu {
	public static CategoryDataset createDataSet(){
		DefaultCategoryDataset dataset=new DefaultCategoryDataset();
		DecimalFormat df=new DecimalFormat("#0.00");
		Sigar sigar=new Sigar();
		
		try{
			Swap swap=sigar.getSwap();
			dataset.addValue(swap.getTotal()/1024/1024,"交换区","总量");
			dataset.addValue(swap.getUsed()/1024/1024,"交换区","使用量");
			dataset.addValue(swap.getFree()/1024/1024,"交换区","剩余量");
	/*		//硬盘 
		    FileSystem fslist[] = sigar.getFileSystemList();  
		    for(int i=0;i<fslist.length;i++){
		    	FileSystem fs=fslist[i];
		    	FileSystemUsage usage=null;
		    	usage=sigar.getFileSystemUsage(fs.getDirName());
		    	 switch (fs.getType()) {  
		            case 0: // TYPE_UNKNOWN ：未知  
		                break;  
		            case 1: // TYPE_NONE  
		                break;  
		            case 2: // TYPE_LOCAL_DISK : 本地硬盘  
		                // 文件系统总大小  
		               dataset.addValue(usage.getTotal()/1024/1024,"文件系统大小","总量");
		                // 文件系统可用大小  
		                dataset.addValue(usage.getAvail()/1024/1024,"文件系统大小","使用量");
		                // 文件系统已经使用量  
		             dataset.addValue( usage.getUsed()/1024/1024,"文件系统大小","剩余量");   
		                break;  
		            case 3:// TYPE_NETWORK ：网络  
		                break;  
		            case 4:// TYPE_RAM_DISK ：闪存  
		                break;  
		            case 5:// TYPE_CDROM ：光驱  
		                break;  
		            case 6:// TYPE_SWAP ：页面交换  
		                break;  
		            }  
		    }*/
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
				"电脑交换区信息",
				"交换区",
				"量(M)",
				createDataSet(),
				PlotOrientation.VERTICAL,
				true,
				false,
				false
				);
		/*	Image image=null;
		try{
			image=ImageIO.read(ChartUtil.class.getResource("test.JPG"));
		}catch(IOException e){
			e.printStackTrace();
		}
		ch.getTitle().setFont(new Font("隶书",Font.BOLD,25));
		ch.getLegend().setItemFont(new Font("宋体",Font.PLAIN,12));
		ch.setBorderVisible(true);
		TextTitle subTitle=new TextTitle("本机信息（内存,交换区，文件）");
		subTitle.setVerticalAlignment(VerticalAlignment.BOTTOM);
		ch.addSubtitle(subTitle);
		CategoryPlot plot=ch.getCategoryPlot();
		plot.setForegroundAlpha(.08F);
		plot.setBackgroundAlpha(0.5F);
		plot.setBackgroundImage(image);
		CategoryAxis categoryAxis=plot.getDomainAxis();
		categoryAxis.setLabelFont(new Font("宋体",Font.PLAIN,12));
		categoryAxis.setTickLabelFont(new Font("宋体",Font.PLAIN,12));
		categoryAxis.setCategoryLabelPositions(CategoryLabelPositions.UP_45);
		ValueAxis valueAxis=plot.getRangeAxis();
		valueAxis.setLabelFont(new Font("宋体",Font.PLAIN,12));
		BarRenderer3D renderer=new BarRenderer3D();
	//	renderer.setItemMargin(0.32);
	//	plot.setRenderer(renderer);*/
		return ch;
		}
	
}

```
