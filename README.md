

#Sample data for available tickets
available_tickets = {
    "movie1": {"name": "Movie One", "available": 50},
    "movie2": {"name": "Movie Two", "available": 30},
    "movie3": {"name": "Movie Three", "available": 20},
}

@app.route('/whatsapp', methods=['POST'])
def whatsapp():
    incoming_msg = request.values.get('Body', '').lower()
    response = MessagingResponse()
    msg = response.message()

    if 'book' in incoming_msg:
        # Example: "Book 2 tickets for movie1"
        parts = incoming_msg.split()
        try:
            num_tickets = int(parts[1])
            movie_id = parts[-1]
            if movie_id in available_tickets and num_tickets <= available_tickets[movie_id]['available']:
                available_tickets[movie_id]['available'] -= num_tickets
                msg.body(f"Successfully booked {num_tickets} tickets for {available_tickets[movie_id]['name']}.")
            else:
                msg.body("Unable to process your request. Check the availability or format.")
        except (IndexError, ValueError):
            msg.body("Please provide the correct booking format: 'Book [number] tickets for [movie ID]'.")
    elif 'show' in incoming_msg:
        # Example: "Show movies"
        movie_list = "\n".join([f"{m['name']} (ID: {id}, Available: {m['available']})" for id, m in available_tickets.items()])
        msg.body(f"Available movies:\n{movie_list}")
    else:
        msg.body("Send 'Show movies' to see available movies or 'Book [number] tickets for [movie ID]' to book tickets.")

    return str(response)

if _name_ == '_main_':
    app.run(debug=True)
