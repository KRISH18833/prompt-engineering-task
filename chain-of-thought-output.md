# Chain-of-Thought Comparison

## Run A: Direct prompt

### Prompt
```
Answer with only the final answer. No explanation, no working.

Problem 1: A store marks a jacket at ₹2,400. It gives a 25% discount, then takes a further ₹150 coupon off the discounted price, then charges 18% GST on the amount after the coupon. Three friends split that total equally, and each also pays a ₹30 delivery fee. How much does each friend pay in total?

Problem 2: A train leaves a station at 9:40 AM travelling at 60 km/h. A second train leaves the same station at 10:10 AM on the same track in the same direction at 90 km/h. At what time does the second train catch the first?
```

### Raw Output
```
Problem 1: ₹679
Problem 2: 11:10 AM
```

## Run B: Step-by-step prompt

### Prompt
```
Solve both problems. Think step by step and show your working before giving each final answer.

Problem 1: A store marks a jacket at ₹2,400. It gives a 25% discount, then takes a further ₹150 coupon off the discounted price, then charges 18% GST on the amount after the coupon. Three friends split that total equally, and each also pays a ₹30 delivery fee. How much does each friend pay in total?

Problem 2: A train leaves a station at 9:40 AM travelling at 60 km/h. A second train leaves the same station at 10:10 AM on the same track in the same direction at 90 km/h. At what time does the second train catch the first?
```

### Raw Output
```
Here are the step-by-step solutions for both problems:

Problem 1

Step 1: Calculate the price after the 25% discount.

  - Marked price = ₹2,400
  - Discount amount = 25\% \times 2,400 = 0.25 \times 2,400 = ₹600
  - Price after discount = 2,400 - 600 = ₹1,800

Step 2: Apply the ₹150 coupon.

  - Price after coupon = 1,800 - 150 = ₹1,650

Step 3: Calculate the 18% GST on the amount after the coupon.

  - GST amount = 18\% \times 1,650 = 0.18 \times 1,650 = ₹297
  - Total cost of the jacket with GST = 1,650 + 297 = ₹1,947

Step 4: Split the cost among the 3 friends and add the individual delivery fee.

  - Share of the jacket cost per friend = \frac{1,947}{3} = ₹649
  - Each friend also pays a ₹30 delivery fee:
    \text{Total per friend} = 649 + 30 = ₹679

Final Answer: Each friend pays ₹679 in total.

Problem 2

Step 1: Find the time difference between the departures of the two trains.

  - Train 1 departs at 9:40 AM.
  - Train 2 departs at 10:10 AM.
  - Time difference =
    30\text{ minutes} = \frac{30}{60}\text{ hours} = 0.5\text{ hours}.

Step 2: Calculate the head start distance of the first train.

  - Speed of Train 1 = 60\text{ km/h}
  - Distance covered in 0.5 hours =
    \text{Speed} \times \text{Time} = 60 \times 0.5 = 30\text{ km}.

Step 3: Determine the relative speed.

  - Speed of Train 2 = 90\text{ km/h}
  - Both trains are travelling in the same direction, so the relative speed is:
    \text{Relative Speed} = 90 - 60 = 30\text{ km/h}

Step 4: Calculate the time required for Train 2 to catch Train 1.

  - \text{Time} = \frac{\text{Distance}}{\text{Relative Speed}} = \frac{30\text{ km}}{30\text{ km/h}} = 1\text{ hour}.

Step 5: Determine the catch-up time.

  - Train 2 starts chasing at 10:10 AM.
  - Catch-up time = 10:10\text{ AM} + 1\text{ hour} = 11:10\text{ AM}.

Final Answer: The second train catches the first at 11:10 AM.
```

## Score
Run A: 2/2 correct. Run B: 2/2 correct.

## Analysis
Accuracy did not improve: both prompts got both problems right. The model likely reasons internally (built-in thinking), so the direct prompt already worked. The step-by-step prompt added visible working that can be checked.
