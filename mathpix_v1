import cv2
import json
import re
import matplotlib.pyplot as plt



# Load the image
image_path = r'page_2.png'
rgb = cv2.imread(image_path)

latex_symbols = [
    "=", "<", ">", "_",
    "^", "|", "\\infty", "\\div", "\\sqrt", "\\pm", "\\mp", "\\cdot",
    "\\neq", "\\geq", "\\leq", "\\theta", "\\lambda", "\\mu", "\\pi",
    "\\alpha", "\\beta", "\\gamma", "\\phi", "\\Sigma", "\\Omega",
    "\\nabla", "\\int", "\\sum", "\\prod", "\\subset", "\\cup", "\\cap",
    "\\rightarrow", "\\leftarrow", "\\Rightarrow", "\\Leftarrow",
    "\\leftrightarrow", "\\Leftrightarrow", "= -", "\\frac", "\\emptyset","\\text","\\quad"
]

def is_number(value):
    try:
        int(value)
        return True
    except ValueError:
        return False

result = {}
# Get the recognized text from the API response
with open("page2.json", "r") as file:
    resultFileStr = file.read()
    result = json.loads(resultFileStr)

count = 0;

for word_entry in result['line_data']:
#     print(word_entry)
    cnt_data = word_entry['cnt']
    if 'text' in word_entry:
        cnt_word = word_entry['text']
#         print('cnt_word',cnt_word)
#         print('length',len(cnt_word))
#         text = cnt_word.split('quad')
#         print(text)
#         print('coordinates',cnt_data)
        #         print(word_entry['confidence'])
        # ... (contains_numbers and other checks)
        contains_math = any(symbol in cnt_word for symbol in latex_symbols)
#         print(contains_math)
        contains_numbers = any(is_number(char) for char in cnt_word)
        if contains_math and contains_numbers:
            latex_line = word_entry.get('text').strip()
            print('Text Identified :: '+ latex_line)
            pattern = r"(?:\$\$[^\$]*\$\$|\\\[.*?\\\]|\\\(.*?\\\)|\\begin{equation}.*?\\end{equation})"
            #r"\\(?:[^a-zA-Z]|[a-zA-Z]+)"
            #r"\\[a-zA-Z]+(?:\{[^}]*\}|\[[^\]]*\])*"
            match = re.search(pattern, latex_line)
            if match:
                start_index = match.start() + 3
                end_index = match.end() - 3
                print('Pattern/Subtring :: '+ latex_line[start_index:end_index])
                print(f"Start Index: {start_index}, End Index: {end_index}")
                start_pixel = 8 * start_index
                end_pixel = 8 * end_index
                c1 = word_entry['cnt'][0];
                c2 = word_entry['cnt'][2]
                print("c1 before ",c1)
                print("c2 before ",c2)
                word_entry['cnt'][0] = [c1[0]+start_pixel,c1[1]-3]
                word_entry['cnt'][2] = [c1[0]+end_pixel,c2[1]+2]
                c1 = word_entry['cnt'][0];
                c2 = word_entry['cnt'][2]
                print("c1 after ",c1)
                print("c2 after ",c2)
                cv2.rectangle(rgb, c1, c2, (0, 0, 255), 1)
                # LaTeX equation code
                latex_code = "$\ "+latex_line[start_index:end_index]+"$"
                #r"$\frac{d^2y}{dx^2} + 2\frac{dy}{dx} + y = 0$"

                # Create a larger figure with adjusted margins
                fig, ax = plt.subplots(figsize=(2, 1))
                fig.subplots_adjust(left=0.5, right=0.9, top=0.5, bottom=0.1)

                # Add a LaTeX equation as an annotation
                ax.annotate(latex_code, xy=(0, 1), fontsize=14, ha='center' , va='center')

                # Turn off the axis
                ax.axis('off')

                # Show the plot
                count = count + 1
                name = 'test/eq'+str(count)
                plt.savefig(name+'.png')
                plt.show()

            else:
                print("Pattern not found")

cv2.imwrite('page2_f1.jpg', rgb)    
print('done')
