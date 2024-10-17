## Exercício em Java para treinar como executar transações de forma responsável para o mySQL com setAutoCommit() e rollback().
### Programa principal:
```java
package application;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;

import db.DB;
import db.DbException;

public class Program {

	public static void main(String[] args) {
	
		Connection conn = null;
		Statement st = null;
		
		try {
			conn = DB.getConnection();
			
			// we need to make sure the program executes the entire code 
			// or doesnt execute not at all
			// all operation are pending waiting for a confirmation
			conn.setAutoCommit(false);
			
			st =conn.createStatement();
			
			int rows1 = st.executeUpdate("UPDATE seller SET baseSalary = 2090 WHERE DepartmentId = 1");
			
			int x = 1;
			if (x < 2) {
				throw new SQLException("Fake error");
			}
			
			int rows2 = st.executeUpdate("UPDATE seller SET baseSalary = 3090 WHERE DepartmentId = 2");
			
			// confirms that the operations are done, so it executes them all
			// so only executes the above code, it there wasn't any error along the way
			conn.commit();
			
			System.out.println("rows1 " + rows1);
			System.out.println("rows2 " + rows2);
		} catch (SQLException e) {
			try {
				// if there's an error along the way, returns the program to the first state
				conn.rollback();
				throw new DbException("Transaction rolled back! Caused by: " + e.getMessage());
			} catch (SQLException e1) {
				// rollback error
				throw new DbException("Error trying to rollback! Caused by: " + e1.getMessage());
			}
		} finally {
			DB.closeStatement(st);
			DB.closeConnection();
		}
		
	}

}

```
