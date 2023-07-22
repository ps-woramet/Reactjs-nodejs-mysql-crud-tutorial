--------------------------------- client ---------------------------------

0. npm create vite@latest
    > react
    > project name: reactjs-nodejs-mysql-crud-tutorial
    > npm install
    > npm run dev

1. สร้าง folders client, server 
    นำไฟล์ทั้งหมดเข้า folder client > cd to client terminal > npm run dev

2. index.html add
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.3.1/dist/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

3. -App.jsx สร้าง form

    import './App.css'

    function App() {

    return (
        <>
        <div className='App container'>
            <h1>Employee Information</h1>
            <div className='information'>
                <form action="">
                    <div className='mb-3'>
                        <label htmlFor="name" className='form-label'>Name</label>
                        <input type='text' className='form-control' placeholder='Enter name'/>
                    </div>
                    <div className='mb-3'>
                        <label htmlFor="age" className='form-label'>Age</label>
                        <input type='number' className='form-control' placeholder='Enter age'/>
                    </div>
                    <div className='mb-3'>
                        <label htmlFor="country" className='form-label'>Country</label>
                        <input type='text' className='form-control' placeholder='Enter country'/>
                    </div>
                    <div className='mb-3'>
                        <label htmlFor="position" className='form-label'>Position</label>
                        <input type='text' className='form-control' placeholder='Enter position'/>
                    </div>
                    <div className='mb-3'>
                        <label htmlFor="wage" className='form-label'>Wage</label>
                        <input type='number' className='form-control' placeholder='Enter wage'/>
                    </div>
                    <button className='btn btn-success'>Add Employee</button>
                </form>
            </div>
            <hr />
            <div className="emplayee">
                <button className='btn btn-primary'>Show employees</button>
            </div>
        </div>
        
        </>
    )
    }

    export default App

--------------------------------- server ---------------------------------

4. install dependencies
    terminal cd to server > npm init
    > npm i cors express mysql nodemon

5. package.json

    "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "nodemon index"
    },

6. web browser > http://localhost/phpmyadmin/

7. สร้างฐานข้อมูล new -> employeeSystem -> utf8_general_ci -> create > sql > 

    CREATE TABLE employees(
        id int(10) NOT NULL PRIMARY KEY AUTO_INCREMENT,
        name varchar(100) NOT NULL,
        age int(10) NOT NULL,
        country varchar(100) NOT NULL,
        position varchar(100) NOT NULL,
        wage int(10) NOT NULL
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    เพิ่มข้อมูล 

    INSERT INTO `employees` (`id`, `name`, `age`, `country`, `position`, `wage`) VALUES (NULL, 'woramet', '21', 'thailand', 'frontend', '10000'), (NULL, 'game', '22', 'english', 'backend', '15000');

8. -index.js สร้าง server ,เชื่อมต่อ database, สร้าง route

    const express = require('express')
    const app = express();
    const mysql = require('mysql');
    const cors = require('cors');

    app.use(cors());
    app.use(express.json());

    const db = mysql.createConnection({
        user: 'root',
        host: 'localhost',
        password: '',
        database: 'employeesystem'
    })

    app.get('/employees', (req, res) => {
        db.query('SELECT * FROM employees', (err, result) => {
            if (err){
                console.log(err)
            }else{
                res.send(result)
            }
        })
    })

    app.listen('3001', () => {
        console.log('server is running on port 3001')
    })

--------------------------------- client ---------------------------------

9. install dependencies
    terminal cd to client > npm i axios
    เพื่อดึงข้อมูลมาจาก server

10. ทำการดึงข้อมูลด้วย axios ที่ app.jsx และสร้าง state Employee, สร้าง function เพื่อ update ค่า state Employee เมื่อกดปุ่ม

    -App.jsx

        import './App.css'
        import Axios from 'axios'
        import {useState} from 'react'

        function App() {

        const [employeeList, SetEmployeeList] = useState([]);

        const getEmployee = () => {
            Axios.get('http://localhost:3001/employees').then((response) => SetEmployeeList(response.data))
        }

        return (
            <>
            <div className='App container'>
                <h1>Employee Information</h1>
                <div className='information'>
                    <form action="">
                        <div className='mb-3'>
                        <label htmlFor="name" className='form-label'>Name</label>
                        <input type='text' className='form-control' placeholder='Enter name'/>
                        </div>
                        <div className='mb-3'>
                        <label htmlFor="age" className='form-label'>Age</label>
                        <input type='number' className='form-control' placeholder='Enter age'/>
                        </div>
                        <div className='mb-3'>
                        <label htmlFor="country" className='form-label'>Country</label>
                        <input type='text' className='form-control' placeholder='Enter country'/>
                        </div>
                        <div className='mb-3'>
                        <label htmlFor="position" className='form-label'>Position</label>
                        <input type='text' className='form-control' placeholder='Enter position'/>
                        </div>
                        <div className='mb-3'>
                        <label htmlFor="wage" className='form-label'>Wage</label>
                        <input type='number' className='form-control' placeholder='Enter wage'/>
                        </div>
                        <button className='btn btn-success'>Add Employee</button>
                    </form>
                </div>
                <hr />
                <div className="emplayee">
                <button className='btn btn-primary' onClick={getEmployee}>Show employees</button>
                <br /><br />
                {employeeList.map((val, key) => {
                    return (
                    <div className='employee card' key={key}>
                        <div className="card-body text-left">
                        <p className="card-text">{val.name}</p>
                        <p className="card-text">{val.age}</p>
                        <p className="card-text">{val.country}</p>
                        <p className="card-text">{val.positon}</p>
                        <p className="card-text">{val.wage}</p>
                        </div>
                    </div>
                    )
                })}
                </div>
            </div>
            </>
        )
        }

        export default App

11. ทำการ สร้าง state name, age, country, position, wage, สร้าง function เพื่อ POST (state employeeList) ไปที่ http://localhost:3001/create เมื่อ click button

    -App.jsx

        import './App.css'
        import Axios from 'axios'
        import {useState} from 'react'

        function App() {

        console.log('render')

        const [employeeList, SetEmployeeList] = useState([]);
        const [name, setName] = useState('')
        const [age, setAge] = useState(0)
        const [country, setCountry] = useState('')
        const [positon, setPosition] = useState('')
        const [wage, setWage] = useState(0)

        const getEmployee = () => {
            Axios.get('http://localhost:3001/employees').then((response) => SetEmployeeList(response.data))
        }

        const addEmployee = () => {
            Axios.post('http://localhost:3001/create',{
            name: name,
            age: age,
            country: country,
            position: positon,
            wage: wage
            }).then(() => {
            SetEmployeeList(
                [...employeeList,
                {
                name: name,
                age: age,
                country: country,
                positon: positon,
                wage: wage
                }])
            })
        }

        return (
            <>
            <div className='App container'>
                <h1>Employee Information</h1>
                <div className='information'>
                <form action="">
                    <div className='mb-3'>
                    <label htmlFor="name" className='form-label'>Name</label>
                    <input type='text' className='form-control' placeholder='Enter name' onChange={(event) => {setName(event.target.value)}}/>
                    </div>
                    <div className='mb-3'>
                    <label htmlFor="age" className='form-label'>Age</label>
                    <input type='number' className='form-control' placeholder='Enter age' onChange={(event) => {setAge(event.target.value)}}/>
                    </div>
                    <div className='mb-3'>
                    <label htmlFor="country" className='form-label'>Country</label>
                    <input type='text' className='form-control' placeholder='Enter country' onChange={(event) => {setCountry(event.target.value)}}/>
                    </div>
                    <div className='mb-3'>
                    <label htmlFor="position" className='form-label'>Position</label>
                    <input type='text' className='form-control' placeholder='Enter position' onChange={(event) => {setPosition(event.target.value)}}/>
                    </div>
                    <div className='mb-3'>
                    <label htmlFor="wage" className='form-label'>Wage</label>
                    <input type='number' className='form-control' placeholder='Enter wage' onChange={(event) => {setWage(event.target.value)}}/>
                    </div>
                    <button className='btn btn-success' onClick={addEmployee}>Add Employee</button>
                </form>
                </div>
                <hr />
                <div className="emplayee">
                <button className='btn btn-primary' onClick={getEmployee}>Show employees</button>
                <br /><br />
                {employeeList.map((val, key) => {
                    return (
                    <div className='employee card' key={key}>
                        <div className="card-body text-left">
                        <p className="card-text">{val.name}</p>
                        <p className="card-text">{val.age}</p>
                        <p className="card-text">{val.country}</p>
                        <p className="card-text">{val.positon}</p>
                        <p className="card-text">{val.wage}</p>
                        </div>
                    </div>
                    )
                })}
                </div>
            </div>
            </>
        )
        }

        export default App

--------------------------------- server ---------------------------------

12. สร้าง route /create post ที่ index.js

    -index.js
        app.post('/create', (req, res) => {
            const name = req.body.name
            const age = req.body.age
            const country = req.body.country
            const position = req.body.position
            const wage = req.body.wage
            
            db.query("INSERT INTO employees (name, age, country, position, wage) VALUES(?, ?, ?, ?, ?)", 
            [name, age, country, position, wage]),
            (err, result) => {
                if (err) {
                    console.log(err)
                } else{
                    res.send('value inserted')
                }
            }
        })

--------------------------------- client ---------------------------------

13. สร้าง state newWage, สร้าง function เพื่อ PUT ค่า wage ไปที่ http://localhost:3001/create เมื่อ click button
    โดยจะรับค่า id มาเพื่อตรวจสอบว่ามีค่าเท่ากับ id ไหนใน employeeList แล้วแก้ค่า wage ใน EmployeeList ที่ id นั้น

    -App.jsx

        const [newWage, setNewWage] = useState(0)

        const updateEmployeeWage = (id) => {
            Axios.put("http://localhost:3001/update", {wage:newWage, id: id}).then((response) => {
            SetEmployeeList(
                employeeList.map((val) => {
                return val.id == id ? {
                    id: val.id,
                    name: val.name,
                    country: val.country,
                    age: val.age,
                    position: val.position,
                    wage: newWage
                } : val;
                })
            )
            })
        }

    ในส่วนของ return มีการ update newWage state เมื่อมีการกรอก input, สร้างปุ่มเพื่อกด click แล้วจะเรียกฟังก์ชั่นโดยจะรับค่า id มาเพื่ออัพเดตค่าใน state employeeList

        {employeeList.map((val, key) => {
        return (
            <div className='employee card' key={key}>
            <div className="card-body text-left">
                <p className="card-text">{val.name}</p>
                <p className="card-text">{val.age}</p>
                <p className="card-text">{val.country}</p>
                <p className="card-text">{val.positon}</p>
                <p className="card-text">{val.wage}</p>
                
                <div className="d-flex">
                <input type='number'
                    style={{width: '300px'}}
                    placeholder='15000...'
                    className='form-control'
                    onChange={(event) => {setNewWage(event.target.value)}}/>
                <button className='btn btn-warning' onClick={() => {updateEmployeeWage(val.id)}}>update</button>
                </div>

            </div>
            </div>
        )
        })}

--------------------------------- server ---------------------------------

14. สร้าง route /update put ที่ index.js

    -index.js

        app.put('/update', (req, res) => {
            const id =req.body.id;
            const wage = req.body.wage;
            db.query("UPDATE employees SET wage = ? WHERE id = ?", [wage, id], (err, result) => {
                if (err){
                    console.log(err)
                }else{
                    res.send(result)
                }
            })
        })

--------------------------------- client ---------------------------------

15. สร้าง function เพื่อ DELETE ค่า employeeList แล้วใช้ method delete ที่ http://localhost:3001/delete/${id} เมื่อ click button
    โดยจะรับค่า id แล้วจากนั้นลบ ค่าใน employeeList

    -App.js

        const deleteEmployee = (id) => {
            Axios.delete(`http://localhost:3001/delete/${id}`).then((response) => {
            SetEmployeeList(employeeList.filter((val) => {
                return val.id != id
            }))
            })
        }

    ในส่วนของ return มีการเพิ่มปุ่มเพื่อเรียกฟังก์ชั่นโดยจะรับค่า id มาเพื่อลบค่าใน state employeeList

        {employeeList.map((val, key) => {
        return (
            <div className='employee card' key={key}>
            <div className="card-body text-left">
                <p className="card-text">{val.name}</p>
                <p className="card-text">{val.age}</p>
                <p className="card-text">{val.country}</p>
                <p className="card-text">{val.positon}</p>
                <p className="card-text">{val.wage}</p>
                
                <div className="d-flex">
                <input type='number'
                    style={{width: '300px'}}
                    placeholder='15000...'
                    className='form-control'
                    onChange={(event) => {setNewWage(event.target.value)}}/>
                <button className='btn btn-warning' onClick={() => {updateEmployeeWage(val.id)}}>update</button>
                <button className='btn btn-danger' onClick={() => {deleteEmployee(val.id)}}>delete</button>
                </div>

            </div>
            </div>
        )
        })}

--------------------------------- server ---------------------------------

16. สร้าง route /delete/:id delete ที่ index.js โดยมีการรับค่า req.params.id เพื่อนำค่า id มาลบข้อมูลจาก database

    -index.js

        app.delete('/delete/:id', (req, res) => {
            const id =req.params.id;
            db.query("DELETE FROM employees WHERE id = ?", id, (err, result) => {
                if (err) {
                    console.log(err)
                }else{
                    res.send(result)
                }
            })
        })