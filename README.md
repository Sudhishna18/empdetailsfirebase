
# Ex.No:1 To create a employee details fields and to display the employee details using Firebase Database in Android Studio.


## AIM:

To create and display the employee details using Firebase Database in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as HelloWorld and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display the employee details in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the DatabaseTable using the firebasedatabase”.
Developed by: Sudhishna P
Registeration Number : 212224040336
*/
```
### ACTIVITY_MAIN.XML:
```XML
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://android.com"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:ignore="HardcodedText,MissingAutofillHints">

    <EditText
        android:id="@+id/etName"
        android:layout_width="match_parent"
        android:layout_height="55dp"
        android:hint="Name"
        android:inputType="textPersonName" />

    <EditText
        android:id="@+id/etAge"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Age"
        android:inputType="number" />

    <EditText
        android:id="@+id/etSalary"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Salary"
        android:inputType="number" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        style="?android:attr/buttonBarStyle">

        <Button
            android:id="@+id/btnAdd"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Add"
            style="?android:attr/buttonBarButtonStyle" />
        <Button
            android:id="@+id/btnUpdate"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Update"
            style="?android:attr/buttonBarButtonStyle" />

        <Button
            android:id="@+id/btnDelete"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Delete"
            style="?android:attr/buttonBarButtonStyle" />
    </LinearLayout>

    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>
```
### MAINACTIVITY.JAVA:
```JAVA
package com.example.firebaseexp;

import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import com.google.firebase.database.DataSnapshot;
import com.google.firebase.database.DatabaseError;
import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.firebase.database.ValueEventListener;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    EditText etName, etAge, etSalary;
    Button btnAdd, btnUpdate, btnDelete;
    ListView listView;
    DatabaseReference dbRef;

    ArrayList<String> items = new ArrayList<>();
    ArrayList<String> keys = new ArrayList<>();
    ArrayAdapter<String> adapter;
    String selectedKey = null;
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etName = findViewById(R.id.etName);
        etAge = findViewById(R.id.etAge);
        etSalary = findViewById(R.id.etSalary);
        btnAdd = findViewById(R.id.btnAdd);
        btnUpdate = findViewById(R.id.btnUpdate);
        btnDelete = findViewById(R.id.btnDelete);
        listView = findViewById(R.id.listView);

        dbRef = FirebaseDatabase.getInstance().getReference("Employees");

        adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, items);
        listView.setAdapter(adapter);

        // Read and listen to data updates
        dbRef.addValueEventListener(new ValueEventListener() {
            @Override
            public void onDataChange(DataSnapshot snapshot) {
                items.clear();
                keys.clear();
                for (DataSnapshot d : snapshot.getChildren()) {
                    // FIX: Changed "jjEmp" to "Emp" to match your actual class file name
                    Emp e = d.getValue(Emp.class);
                    if (e != null) {
                        // FIX: Updated variable references to use standard getters: getName(), getAge(), getSalary()
                        items.add(e.getName() + " - " + e.getAge() + " - " + e.getSalary());
                        keys.add(d.getKey());
                    }
                }
                adapter.notifyDataSetChanged();
            }
   @Override
            public void onCancelled(DatabaseError error) {
            }
        });

        // Add Employee
        btnAdd.setOnClickListener(v -> {
            Emp e = new Emp(etName.getText().toString(), etAge.getText().toString(), etSalary.getText().toString());
            dbRef.push().setValue(e);
            clearFields();
        });

        // Update selected Employee
        btnUpdate.setOnClickListener(v -> {
            if (selectedKey != null) {
                Emp e = new Emp(etName.getText().toString(), etAge.getText().toString(), etSalary.getText().toString());
                dbRef.child(selectedKey).setValue(e);
                clearFields();
            } else {
                Toast.makeText(this, "Select an item first", Toast.LENGTH_SHORT).show();
            }
        });

        // Delete selected Employee
        btnDelete.setOnClickListener(v -> {
            if (selectedKey != null) {
                dbRef.child(selectedKey).removeValue();
                clearFields();
            } else {
                Toast.makeText(this, "Select an item first", Toast.LENGTH_SHORT).show();
            }
        });
        // Select item from list to edit or delete
        listView.setOnItemClickListener((p, v, pos, id) -> {
            selectedKey = keys.get(pos);
            dbRef.child(selectedKey).get().addOnSuccessListener(d -> {
                Emp e = d.getValue(Emp.class);
                if (e != null) {
                    // FIX: Updated to use standard getters: getName(), getAge(), getSalary()
                    etName.setText(e.getName());
                    etAge.setText(e.getAge());
                    etSalary.setText(e.getSalary());
                }
            });
        });
    }

    private void clearFields() {
        etName.setText("");
        etAge.setText("");
        etSalary.setText("");
        selectedKey = null;
    }
}
```





## OUTPUT
<img width="1915" height="1140" alt="Screenshot 2026-11-20 110149" src="https://github.com/user-attachments/assets/6630f36f-344d-4c13-9932-109bfe28edd1" />
<img width="1916" height="1078" alt="Screenshot 2026-11-20 110201" src="https://github.com/user-attachments/assets/37eb48ed-d683-4a08-8f5a-9f7121ffd085" />




## RESULT
Thus a Simple Android Application create a firebase database and to display the employee details using Firbase Real Time Database in Android Studio is developed and executed successfully.
