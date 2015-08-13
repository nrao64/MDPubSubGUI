import zmq
import tkinter
import threading
import time

context = zmq.Context()
grid_rcv = context.socket(zmq.SUB)
grid_rcv.connect('tcp://10.10.100.143:11111')
grid_rcv.setsockopt_string(zmq.SUBSCRIBE, '')
root = tkinter.Tk()
data = {}
labels = {}


def set_grid():
    label_1 = tkinter.Label(root, text='Symbol')
    label_2 = tkinter.Label(root, text='Bid Size')
    label_3 = tkinter.Label(root, text='Bid Price')
    label_4 = tkinter.Label(root, text='Ask Price')
    label_5 = tkinter.Label(root, text='Ask Size')
    label_6 = tkinter.Label(root, text='Last Size')
    label_7 = tkinter.Label(root, text='Last Price')
    label_1.grid(row=0)
    label_2.grid(row=0, column=1)
    label_3.grid(row=0, column=2)
    label_4.grid(row=0, column=3)
    label_5.grid(row=0, column=4)
    label_6.grid(row=0, column=5)
    label_7.grid(row=0, column=6)
    root.title('MD Ticker')


def update_data():
    while True:
        data1 = grid_rcv.recv_json()
        bid = get_bid(data1)
        ask = get_ask(data1)
        symbol = data1["SYM"]
        bid_price = bid[0]
        bid_size = bid[1]
        ask_price = ask[0]
        ask_size = ask[1]
        data[symbol] = [bid_size, bid_price, ask_price, ask_size]  # , data1['LASTSZ'], data1['LASTPRC']]
        if data1['LASTSZ'] != 0:
            data[symbol].append(data1['LASTSZ'])
            data[symbol].append(data1['LASTPRC'])
        print(sorted(data.keys()))


def update_gui():
    set_labels()
    while True:
        row = 1
        for key2 in sorted(data.keys()):
            data_value = data[key2]
            column = 1
            for item in data_value:
                labels[row * 10 + column].config(text='%s' % item)
                labels[row * 10 + column].grid(row=row, column=column)
                column += 1
            row += 1
        time.sleep(.1)


def set_labels():
    row = 1
    for _ in data.keys():
        column = 1
        for x in data.values():
            labels[row * 10 + column] = tkinter.Label(root, text='0', font='Verdana 8')
            column += 1
        row += 1

    row = 1
    column = 5
    for a in labels.keys():
        labels[a].config(text='0')
        if column == 6:
            labels[a].config(text='0')
            column = 5
            row += 1

    row = 1

    for key1 in sorted(data.keys()):  # sorted
        column = 0
        label = tkinter.Label(root, text='%s' % key1, font='Verdana 8 bold')
        if row % 2 == 0:
            label.config(bg='light gray', width=8)
        label.grid(row=row, column=column)
        row += 1

    for a in list(labels.keys()):
        for _ in range(1, 6):
            if a % 20 <= 6:
                labels[a].config(bg='light gray', width=8)


def get_bid(data1):
    bid = [0, 0]
    if data1['NUMB'] > 0:
        bid_dict = data1['BID'][0]
        bid_price = bid_dict["BPRC"]
        bid_size = bid_dict["BSZ"]
        bid = [bid_price, bid_size]
    return bid


def get_ask(data1):
    ask = [0, 0]
    if data1['NUMA'] > 0:
        ask_dict = data1['ASK'][0]
        ask_price = ask_dict["APRC"]
        ask_size = ask_dict["ASZ"]
        ask = [ask_price, ask_size]
    return ask


set_grid()
data_thread = threading.Thread(target=update_data, args=())
data_thread.start()
time.sleep(35)
gui_thread = threading.Thread(target=update_gui, args=())
gui_thread.start()
root.mainloop()
