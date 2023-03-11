import java.sql.*;
import java.io.*;
import java.util.*;
class OracleCon{
	public static void main(String args[]){
		Scanner sc=new Scanner(System.in);
		try{
			Class.forName("oracle.jdbc.driver.OracleDriver");
			Connection con=DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:xe","system","daniel");
			Statement stmt=con.createStatement();
			System.out.println("Basic Operations!!!");
			String query="";
			int ch;
			do{
				System.out.println("1. Create");
				System.out.println("2. Insert");
				System.out.println("3. Read");
				System.out.println("4. Update");
				System.out.println("5. Delete");
				System.out.println("6. Call Procedure");
				System.out.println("7. Call Function");
				System.out.println("8. Exit");
				System.out.print("Enter your choice : ");
				ch=sc.nextInt();
				switch(ch){
					case 1:
						System.out.println("create table student(REGNO varchar(10) primary key,STUD_NAME varchar(30),DEPT varchar(10))");
						query="create table student(REGNO varchar(10) primary key,STUD_NAME varchar(30),DEPT varchar(10))";
						stmt.executeUpdate(query);
						System.out.println("Table created successfully");
						System.out.println();
						break;
					case 2:
						PreparedStatement psmt=con.prepareStatement("insert into STUDENT values(?,?,?)");
						BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
						while(true) {
							System.out.print("Enter REGNO : ");
							String regno=br.readLine();
					
							System.out.print("Enter NAME : ");
							String name=br.readLine();
					
							System.out.print("Enter DEPARTMENT : ");
							String dept=br.readLine();
					
							psmt.setString(1,regno);
							psmt.setString(2,name);
							psmt.setString(3,dept);
					
							int count=psmt.executeUpdate();
							if(count>0)
								System.out.println(count+" record inserted");
							else
								System.out.println("No record inserted");
					
							System.out.print("Do you want to insert more records[Yes/No] : ");
					
							String ch1=br.readLine();
							if(ch1.equalsIgnoreCase("no"))
								break;
						}
						System.out.println();
						break;
					case 3:
						ResultSet rs=stmt.executeQuery("select * from student");
						while(rs.next()) {
							System.out.println(rs.getString(1)+"  "+rs.getString(2)+"  "+rs.getString(3));
						}
						System.out.println();
						break;
					case 4:
						BufferedReader br1=new BufferedReader(new InputStreamReader(System.in));
			
						System.out.print("Enter REGNO : ");
						String value1=br1.readLine();
					
						System.out.print("Enter DEPARTMENT : ");
						String value2=br1.readLine();
			
						int count=stmt.executeUpdate("update student set DEPT ='" +value2+"'"+ "where REGNO ='"+value1+"'");
						if(count>0)
							System.out.println(count+" rows updated");
						else
							System.out.println("No record found");
						System.out.println();
						break;
					case 5:
						//BufferedReader br2=new BufferedReader(new InputStreamReader(System.in));
						Scanner s1=new Scanner(System.in);
						while(true) {
							System.out.print("Enter RegNo of Student which is to be deleted : ");
							String value3=s1.next();
							System.out.print(value3);
							int count1=stmt.executeUpdate("delete from student where REGNO='"+value3+"'");
							if(count1==1)
								System.out.println(count1+" rows deleted");
							else
								System.out.println("No record deleted");
			
							System.out.print("Do you want to delete more records[Yes/No] : ");
			
							String ch2=s1.next();
							if(ch2.equalsIgnoreCase("no"))
								break;
				
						}
						System.out.println();
						break;
					case 6:
						System.out.println("Procedure to Enroll New Students");
						CallableStatement ctmt=con.prepareCall("{call INSERTUSER(?,?,?)}");
						Scanner s=new Scanner(System.in);
						System.out.print("Enter the REGNO : ");
						String regno=s.nextLine();
						ctmt.setString(1,regno);
						System.out.print("Enter the NAME : ");
						String name=s.nextLine();
						ctmt.setString(2,name);
						System.out.print("Enter the DEPARTMENT : ");
						String dept=s.nextLine();
						ctmt.setString(3,dept);
						ctmt.execute();
						System.out.println("success");
						System.out.println();
						System.out.println("Procedure to Update Name of Student");
						Scanner s2=new Scanner(System.in);
						System.out.print("Enter the RegNo of student to be updated : ");
						String regno1=s.next();
						System.out.print("Enter the New Name : ");
						String name1=s.next();
						CallableStatement ctmt1=con.prepareCall("{call UPDATER(?,?)}");
						ctmt1.setString(1,regno1);
						ctmt1.setString(2,name1);
						ctmt1.execute();
						System.out.println();
						break;
					case 7:
						System.out.println("Function to Calculate the total number of Student");
						CallableStatement ctmt2=con.prepareCall("{?= call TotalRecord}");
						ctmt2.registerOutParameter(1,Types.VARCHAR);
						ctmt2.execute();
						System.out.println("Total number of Students :"+ctmt2.getString(1));
						System.out.println();
						System.out.println("Function to Display the name of Student");
						BufferedReader br4=new BufferedReader(new InputStreamReader(System.in));
						System.out.print("Enter REGNO : ");
						String prodid2=br4.readLine();
						CallableStatement ctmt3=con.prepareCall("{?= call DisName(?)}");
						ctmt3.setString(2,prodid2);
						ctmt3.registerOutParameter(1,Types.VARCHAR);
						ctmt3.execute();
						System.out.println("The name of the customer is "+ctmt3.getString(1));
						System.out.println();
						break;
				}
			}while(ch!=8);
			con.close();
		}catch(Exception e){ System.out.println(e);}

	}
}
