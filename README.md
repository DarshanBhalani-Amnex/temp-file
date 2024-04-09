using Amnex_Project_Resource_Mapping_System.Models;
using Microsoft.AspNetCore.Mvc;
using Npgsql;

namespace Amnex_Project_Resource_Mapping_System.Controllers
{
    public class AccountController : Controller
    {
        private readonly NpgsqlConnection _connection;

        public AccountController(NpgsqlConnection connection)
        {
            _connection = connection;
            _connection.Open();
        }
        public IActionResult Login()
        {
            return View();
        }


        [HttpPost]
        public IActionResult Login(Login data)
        {
            int userId = -1;
            string userName = null;

            using (var command = new NpgsqlCommand($"SELECT * FROM validateusercredentials('{data.Username}','{data.Password}');", _connection))
            {
                var reader = command.ExecuteReader();
                if (reader.Read())
                {
                    Console.WriteLine(reader.GetInt32(0));
                    Console.WriteLine(reader.GetString(1));
                }
            }
            return Json(new { success = true });
        }
    }
}




@model Login
@{
    Layout = null;
}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>

    <title>Your Website Title</title>
    <style>
        .main-container {
            height: 100vh;
            width: 100vw;
            background: url('../assets/images/bg.jpg');
            color: white;
            background-repeat: no-repeat;
            background-size: cover;
        }

        .login-modal {
            color: black !important;
        }
    </style>
</head>
<body>

    <div class="container-fluid d-flex justify-content-center align-items-center flex-column main-container text-center">
        <h1>Welcome to PRMS</h1>
        <p>Efficiently manage and allocate company resources <br />with our Project Resource Mapping System.<br /> Track current allocations and easily adjust as per project requirements.</p>
        <button id="loginBtnClick" class="btn btn-primary" data-bs-toggle="modal" data-target="#loginModal">Get Started</button>

    </div>
    @* login-modal *@

    <div class="modal fade" id="loginModal" tabindex="-1" role="dialog" aria-labelledby="loginModalLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
                <div class="container-fluid">
                    <div class="bg-light rounded h-100 p-4 login-modal">
                        <h3 class="text-center">Admin Login</h3>
                        <p class="mb-4 text-center">We need to verify you before getting started!</p>
                        <form id="login-form">
                            <div class="mb-3">
                                <label for="Username" class="form-label">User Name</label>
                                <input type="text" class="form-control" id="Username" asp-for="Username" />
                            </div>
                            <div class="mb-1">
                                <label for="Password" class="form-label">Password</label>
                                <input type="password" class="form-control" id="Password" asp-for="Password">
                            </div>
                            <div class="text-end mb-3">Forgot Password ?</div>
                            <button type="submit" class="btn btn-primary px-5 text-center w-100 mt-3">Sign in</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
<script>


    $(document).ready(function () {
        $("#loginBtnClick").click(function () {
            $("#loginModal").modal('show');
        });

        $('#login-form').submit(function (e) {
            e.preventDefault(); // Prevent the default form submission

            var data = {
                username: $('#Username').val(),
                password: $('#Password').val()
            };
           
            $.ajax({
                type: 'POST',
                url: '/Account/Login',
                data: data,
                success: function (response) {
                    window.location.href= '/Home/Dashboard'
                }
            });
        });
    });


   

</script>
