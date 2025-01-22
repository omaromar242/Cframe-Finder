local player = game.Players.LocalPlayer

-- Create ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ModelCFrameTool"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Create Frame
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 250)
frame.Position = UDim2.new(0.5, -150, 0.5, -125)
frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
frame.BorderSizePixel = 2
frame.Visible = true
frame.Parent = screenGui

-- Make the frame draggable
local dragging
local dragStart
local startPos

frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = frame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

frame.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Create TextBox for search
local textBox = Instance.new("TextBox")
textBox.Size = UDim2.new(0.8, 0, 0.2, 0)
textBox.Position = UDim2.new(0.1, 0, 0.1, 0)
textBox.PlaceholderText = "Search Model"
textBox.Text = ""
textBox.TextColor3 = Color3.fromRGB(255, 255, 255)
textBox.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
textBox.Font = Enum.Font.SourceSans
textBox.TextSize = 16
textBox.ClearTextOnFocus = false
textBox.Parent = frame

-- Create ListBox for model selection
local listBox = Instance.new("ScrollingFrame")
listBox.Size = UDim2.new(0.8, 0, 0.4, 0)
listBox.Position = UDim2.new(0.1, 0, 0.35, 0)
listBox.BackgroundTransparency = 1
listBox.ScrollBarThickness = 5
listBox.Parent = frame

-- Create Copy CFrame Button
local copyButton = Instance.new("TextButton")
copyButton.Size = UDim2.new(0.8, 0, 0.2, 0)
copyButton.Position = UDim2.new(0.1, 0, 0.8, 0)
copyButton.Text = "Copy CFrame"
copyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
copyButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
copyButton.Font = Enum.Font.SourceSans
copyButton.TextSize = 16
copyButton.Parent = frame

-- Create Hide Button
local hideButton = Instance.new("TextButton")
hideButton.Size = UDim2.new(0, 100, 0, 50)
hideButton.Position = UDim2.new(1, -110, 0, 10) -- Top-right corner
hideButton.AnchorPoint = Vector2.new(1, 0) -- Align to top-right
hideButton.Text = "Hide"
hideButton.TextColor3 = Color3.fromRGB(255, 255, 255)
hideButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
hideButton.Font = Enum.Font.SourceSans
hideButton.TextSize = 16
hideButton.Parent = screenGui

-- Create Show Button
local showButton = Instance.new("TextButton")
showButton.Size = UDim2.new(0, 100, 0, 50)
showButton.Position = UDim2.new(1, -110, 0, 10) -- Top-right corner
showButton.AnchorPoint = Vector2.new(1, 0) -- Align to top-right
showButton.Text = "Show"
showButton.TextColor3 = Color3.fromRGB(255, 255, 255)
showButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
showButton.Font = Enum.Font.SourceSans
showButton.TextSize = 16
showButton.Visible = false -- Initially hidden
showButton.Parent = screenGui

-- Toggle visibility functions
hideButton.MouseButton1Click:Connect(function()
    frame.Visible = false
    hideButton.Visible = false
    showButton.Visible = true
end)

showButton.MouseButton1Click:Connect(function()
    frame.Visible = true
    hideButton.Visible = true
    showButton.Visible = false
end)

-- Function to update the ListBox with models
local function updateListBox()
    -- Clear previous items
    for _, child in pairs(listBox:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    -- Add new models based on the search
    local searchTerm = textBox.Text:lower()
    for _, model in pairs(workspace.Towers:GetChildren()) do
        if model:IsA("Model") then
            local modelName = model.Name:lower()
            if modelName:find(searchTerm) then
                local button = Instance.new("TextButton")
                button.Size = UDim2.new(1, 0, 0, 30)
                button.Text = model.Name
                button.TextColor3 = Color3.fromRGB(255, 255, 255)
                button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                button.Font = Enum.Font.SourceSans
                button.TextSize = 14
                button.Parent = listBox

                -- When the model is selected, set the selected model
                button.MouseButton1Click:Connect(function()
                    textBox.Text = model.Name
                end)
            end
        end
    end
end

-- Call the function to initially populate the list
updateListBox()

-- Update ListBox when search text changes
textBox.Changed:Connect(function()
    updateListBox()
end)

-- Function to copy CFrame
copyButton.MouseButton1Click:Connect(function()
    local modelName = textBox.Text
    if modelName ~= "" then
        local model = workspace.Towers:FindFirstChild(modelName)
        if model and model:FindFirstChild("HumanoidRootPart") then
            local cframe = model.HumanoidRootPart.CFrame
            setclipboard(tostring(cframe))
            textBox.Text = "CFrame copied!"
        else
            textBox.Text = "Model or HumanoidRootPart not found!"
        end
    else
        textBox.Text = "Select a model first!"
    end
end)
