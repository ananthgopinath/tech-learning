<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask Form</title>
    <style>
        /* General Styles */
        body {
            font-family: "Arial", sans-serif;
            background-color: #f4f7f9;
            margin: 20px;
            color: #333;
        }
        h2 {
            text-align: center;
            color: #2c3e50;
        }
        
        .container {
            display: flex;
            width: 80%;
            margin: auto;
            background: #ffffff;
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
            border-radius: 10px;
            overflow: hidden;
        }

        /* Left Panel - Form */
        .left-panel {
            width: 40%;
            padding: 20px;
            background-color: #ecf0f1;
            border-right: 2px solid #bdc3c7;
        }
        label {
            font-weight: bold;
            color: #34495e;
        }
        input[type="radio"] {
            margin-right: 5px;
        }
        textarea {
            width: 100%;
            height: 100px;
            border: 1px solid #bdc3c7;
            border-radius: 5px;
            padding: 8px;
        }
        input[type="submit"] {
            width: 100%;
            background-color: #2ecc71;
            color: white;
            padding: 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            margin-top: 10px;
        }
        input[type="submit"]:hover {
            background-color: #27ae60;
        }

        /* Right Panel - Results */
        .right-panel {
            width: 60%;
            padding: 20px;
            background-color: #ffffff;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
        }
        th, td {
            border: 1px solid #bdc3c7;
            padding: 12px;
            text-align: left;
        }
        th {
            background-color: #3498db;
            color: white;
        }
        tr:nth-child(even) {
            background-color: #ecf0f1;
        }
    </style>
</head>
<body>

    <h2>Welcome, {{ sm_user }}</h2>

    <div class="container">
        <!-- Left Panel: Form -->
        <div class="left-panel">
            <form action="/" method="POST">
                <label>Choose Environment:</label><br>
                <input type="radio" name="env" value="Prod" required> Prod
                <input type="radio" name="env" value="UAT"> UAT
                <br><br>

                <label>Choose Identifier:</label><br>
                <input type="radio" name="identifier" value="name" required> Name
                <input type="radio" name="identifier" value="ip"> IP
                <input type="radio" name="identifier" value="instance_id"> Instance ID
                <br><br>

                <label>Assets:</label><br>
                <textarea name="assets" required></textarea>
                <br><br>

                <input type="submit" value="Submit">
            </form>
        </div>

        <!-- Right Panel: Results -->
        <div class="right-panel">
            {% if results %}
                <h3>Results</h3>
                <table>
                    <tr>
                        <th>Environment</th>
                        <th>Identifier</th>
                        <th>Assets</th>
                    </tr>
                    {% for row in results %}
                    <tr>
                        <td>{{ row["Environment"] }}</td>
                        <td>{{ row["Identifier"] }}</td>
                        <td>{{ row["Assets"] }}</td>
                    </tr>
                    {% endfor %}
                </table>
            {% endif %}
        </div>
    </div>

</body>
</html>
