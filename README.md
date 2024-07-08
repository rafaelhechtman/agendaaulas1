<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agenda de Aulas</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .container {
            max-width: 800px;
            background-color: white;
            padding: 20px;
            margin-top: 20px;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }
        h1 {
            color: #1E90FF;
        }
        .instructions {
            font-size: 1.2em;
            margin-bottom: 20px;
        }
        #calendar {
            max-width: 100%;
            margin-bottom: 20px;
        }
        .time-slot {
            background-color: #1E90FF;
            color: white;
            border: none;
            padding: 10px;
            margin: 5px;
            border-radius: 5px;
            cursor: pointer;
        }
        .time-slot.booked {
            background-color: #808080;
            cursor: not-allowed;
        }
        .time-slot:hover:not(.booked) {
            background-color: #1C86EE;
        }
        .time-slots {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
        }
        .confirmation {
            margin-top: 20px;
            padding: 10px;
            background-color: #dff0d8;
            border: 1px solid #d6e9c6;
            color: #3c763d;
            border-radius: 5px;
            display: none;
        }
        .cancel-button {
            background-color: #FF6347;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 20px;
        }
        .cancel-button:hover {
            background-color: #FF4500;
        }
        .title {
            font-size: 2em;
            color: #1E90FF;
            margin-bottom: 20px;
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgb(0,0,0);
            background-color: rgba(0,0,0,0.4);
            padding-top: 60px;
        }
        .modal-content {
            background-color: #fefefe;
            margin: 5% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 80%;
            max-width: 600px;
            border-radius: 5px;
        }
        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
        }
        .close:hover,
        .close:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/fullcalendar/3.10.2/fullcalendar.min.css" />
    <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.24.0/moment.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fullcalendar/3.10.2/fullcalendar.min.js"></script>
</head>
<body>
    <div class="container">
        <h1 class="title">Esther Edler Hechtman - Aulas particulares</h1>
        <p class="instructions">Selecione uma data no calendário e escolha um horário disponível para agendar sua aula.</p>
        <div id="calendar"></div>
        <div class="time-slots" id="timeSlots"></div>
        <div class="confirmation" id="confirmation"></div>
        <button class="cancel-button" id="cancelBooking" style="display: none;">Cancelar horário</button>
    </div>

    <!-- The Modal -->
    <div id="myModal" class="modal">
        <div class="modal-content">
            <span class="close">&times;</span>
            <div class="form-container" id="formContainer">
                <input type="text" id="fullName" placeholder="Nome Completo" required>
                <input type="text" id="phoneNumber" placeholder="Número de Telefone" required>
                <input type="text" id="schoolName" placeholder="Escola" required>
                <button id="confirmBooking">Confirmar horário</button>
            </div>
        </div>
    </div>

    <script>
        $(document).ready(function() {
            const reservedSlots = {};
            const modal = document.getElementById("myModal");
            const span = document.getElementsByClassName("close")[0];

            $('#calendar').fullCalendar({
                header: {
                    left: 'prev,next today',
                    center: 'title',
                    right: 'month,agendaWeek,agendaDay'
                },
                selectable: true,
                selectHelper: true,
                dayClick: function(date) {
                    displayTimeSlots(date.format());
                }
            });

            function displayTimeSlots(date) {
                const timeSlotsContainer = $('#timeSlots');
                timeSlotsContainer.empty();
                const times = ['08:00', '09:00', '10:00', '11:00', '12:00', '13:00', '14:00', '15:00', '16:00', '17:00'];

                if (reservedSlots[date]) {
                    $('#confirmation').text(`Você já tem um horário marcado para ${date} às ${reservedSlots[date][0]}.`).show();
                    $('#cancelBooking').show();
                    return;
                } else {
                    $('#confirmation').hide();
                    $('#cancelBooking').hide();
                }

                times.forEach(time => {
                    const slot = $('<button></button>')
                        .addClass('time-slot')
                        .text(time)
                        .attr('data-time', time)
                        .attr('data-date', date);

                    if (reservedSlots[date] && reservedSlots[date].includes(time)) {
                        slot.addClass('booked');
                    }

                    slot.on('click', function() {
                        if (!$(this).hasClass('booked')) {
                            modal.style.display = "block";
                            $('#confirmBooking').off('click').on('click', function() {
                                confirmBooking(date, time);
                            });
                        }
                    });

                    timeSlotsContainer.append(slot);
                });
            }

            function confirmBooking(date, time) {
                const fullName = $('#fullName').val();
                const phoneNumber = $('#phoneNumber').val();
                const schoolName = $('#schoolName').val();

                if (!fullName || !phoneNumber || !schoolName) {
                    alert('Por favor, preencha todos os campos.');
                    return;
                }

                if (!reservedSlots[date]) {
                    reservedSlots[date] = [];
                }
                reservedSlots[date].push(time);

                $('#timeSlots .time-slot').each(function() {
                    if ($(this).data('time') === time && $(this).data('date') === date) {
                        $(this).addClass('booked');
                    }
                });

                $('#confirmation').text(`Sua aula foi agendada para ${date} às ${time}`).show();
                modal.style.display = "none";
                $('#cancelBooking').show().off('click').on('click', function() {
                    cancelBooking(date, time);
                });

                // Add event to the calendar
                $('#calendar').fullCalendar('renderEvent', {
                    title: `${fullName} às ${time}`,
                    start: date + 'T' + time,
                    allDay: false,
                    backgroundColor: '#FF6347',
                    borderColor: '#FF6347'
                });

                // Reset form fields
                $('#fullName').val('');
                $('#phoneNumber').val('');
                $('#schoolName').val('');

                // Send SMS notification
                sendSMSNotification(fullName, phoneNumber, schoolName, date, time);
            }

            function cancelBooking(date, time) {
                reservedSlots[date] = reservedSlots[date].filter(slot => slot !== time);

                $('#confirmation').text(`Sua reserva para ${date} às ${time} foi cancelada.`).show();
                $('#cancelBooking').hide();
                displayTimeSlots(date);

                // Remove event from the calendar
                $('#calendar').fullCalendar('removeEvents', function(event) {
                    return event.start.format() === date + 'T' + time;
                });
            }

            function sendSMSNotification(fullName, phoneNumber, schoolName, date, time) {
                $.ajax({
                    url: 'https://your-backend-url/send-sms',
                    method: 'POST',
                    data: {
                        fullName: fullName,
                        phoneNumber: phoneNumber,
                        schoolName: schoolName,
                        date: date,
                        time: time
                    },
                    success: function(response) {
                        console.log('SMS enviado com sucesso:', response);
                    },
                    error: function(error) {
                        console.error('Erro ao enviar SMS:', error);
                    }
                });
            }

            // Close modal on X button click
            span.onclick = function() {
                modal.style.display = "none";
            }

            // Close modal on outside click
            window.onclick = function(event) {
                if (event.target == modal) {
                    modal.style.display = "none";
                }
            }
        });
    </script>
</body>
</html>
