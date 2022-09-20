# Firebase-in-android
```
Developed By : Loghul M

Reg.no : 212220230029

```


## Activity_main.xml

```java


<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">


    <EditText
        android:id="@+id/edit_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPersonName"
        android:hint="Name"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.497"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.3" />

    <EditText
        android:id="@+id/edit_position"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPersonName"
        android:hint="Position"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.497"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.448" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Insert"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.281"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.639" />

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Update"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.798"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.639" />

    <Button
        android:id="@+id/button3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Remove"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.564"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.758" />


</androidx.constraintlayout.widget.ConstraintLayout>



```


## MainActivity.java:

```java

package com.example.firebase;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;


import java.util.HashMap;


public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        final EditText edit_name = findViewById(R.id.edit_name);
        final EditText edit_position = findViewById(R.id.edit_position);
        Button insert = findViewById(R.id.button);
        Button update = findViewById(R.id.button2);
        Button remove = findViewById(R.id.button3);
        DAOEmployee dao =new DAOEmployee();

        insert.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Employee emp = new Employee(edit_name.getText().toString(), edit_position.getText().toString());

                dao.add(emp).addOnSuccessListener(suc ->
                {
                    Toast.makeText(MainActivity.this, "Record is inserted", Toast.LENGTH_SHORT).show();
                }).addOnFailureListener(er ->
                {
                    Toast.makeText(MainActivity.this, ""+ er.getMessage(), Toast.LENGTH_SHORT).show();
                });
            }
        });
        update.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                HashMap<String, Object> hashMap = new HashMap<>();
                hashMap.put("name", edit_name.getText().toString());
                hashMap.put("position", edit_position.getText().toString());
                dao.update("-NBi1u6SrnPpv0ULy-Bp", hashMap).addOnSuccessListener(suc ->
                {
                    Toast.makeText(MainActivity.this, "Record is updated", Toast.LENGTH_SHORT).show();
                    finish();
                }).addOnFailureListener(er ->
                {
                    Toast.makeText(MainActivity.this, ""+er.getMessage(), Toast.LENGTH_SHORT).show();

                });
            }
        });

       remove.setOnClickListener(new View.OnClickListener() {
           @Override
           public void onClick(View view) {
               dao.remove("-NBi1u6SrnPpv0ULy-Bp").addOnSuccessListener(suc ->
               {
                   Toast.makeText(MainActivity.this, "Record is removed", Toast.LENGTH_SHORT).show();
                   finish();
               }).addOnFailureListener(er ->
               {
                   Toast.makeText(MainActivity.this, ""+er.getMessage(), Toast.LENGTH_SHORT).show();

               });

           }
       });





    }
}


```


## Employee.java

```java

package com.example.firebase;
import com.google.firebase.database.Exclude;

import java.io.Serializable;

public class Employee implements Serializable
{


    @Exclude

    private String name;
    private String position;
    public Employee(){}
    public Employee(String name, String position)
    {
        this.name = name;
        this.position = position;
    }

    public String getName()
    {
        return name;
    }

    public void setName(String name)
    {
        this.name = name;
    }

    public String getPosition()
    {
        return position;
    }


}




```




## DAOEmployee.java:

```java

package com.example.firebase;

import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.android.gms.tasks.Task;

import java.util.HashMap;

public class DAOEmployee {

    private DatabaseReference databaseReference;
    public DAOEmployee()
    {
        FirebaseDatabase db =FirebaseDatabase.getInstance();
        databaseReference = db.getReference(Employee.class.getSimpleName());
    }
    public Task<Void> add(Employee emp)
    {
        return databaseReference.push().setValue(emp);
    }
    public Task<Void> update(String key, HashMap<String ,Object> hashMap)
    {
        return databaseReference.child(key).updateChildren(hashMap);
    }
    public Task<Void> remove(String key)
    {
        return databaseReference.child(key).removeValue();
    }

}



```


## Output
![WhatsApp Image 2022-09-20 at 3 05 55 PM](https://user-images.githubusercontent.com/78194419/191234475-c21e596f-6497-4d7c-b6fe-a53b989c6d53.jpeg)
![WhatsApp Image 2022-09-20 at 3 05 56 PM](https://user-images.githubusercontent.com/78194419/191234519-cb7e5fa7-e91e-4af6-9d81-72c4b0dfcce9.jpeg)



