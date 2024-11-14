-- Solara Library for Roblox
-- Ultra-professional design inspired by Kavo-Ui and Orion with more animations and sleek styles, now with a beautiful purple theme

local SolaraLibrary = {}
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")

local theme = {
    Main = Color3.fromRGB(45, 15, 75),
    Secondary = Color3.fromRGB(55, 20, 90),
    Accent = Color3.fromRGB(145, 70, 255),
    Stroke = Color3.fromRGB(75, 30, 110),
    Divider = Color3.fromRGB(100, 50, 150),
    Text = Color3.fromRGB(240, 240, 255),
    TextDark = Color3.fromRGB(180, 180, 200)
}

function SolaraLibrary:CreateWindow(title)
    local ScreenGui = Instance.new("ScreenGui")
    local MainFrame = Instance.new("Frame")
    local Title = Instance.new("TextLabel")
    local MinimizeButton = Instance.new("TextButton")
    local Toggle = false

    ScreenGui.Name = "SolaraLibrary"
    ScreenGui.Parent = CoreGui

    MainFrame.Name = "MainFrame"
    MainFrame.Parent = ScreenGui
    MainFrame.BackgroundColor3 = theme.Main
    MainFrame.BorderSizePixel = 0
    MainFrame.Position = UDim2.new(0.3, 0, 0.3, 0)
    MainFrame.Size = UDim2.new(0, 500, 0, 350)
    MainFrame.ClipsDescendants = true

    MainFrame:TweenPosition(UDim2.new(0.5, -250, 0.5, -175), "Out", "Quad", 1.5, true)

    Title.Name = "Title"
    Title.Parent = MainFrame
    Title.BackgroundColor3 = theme.Secondary
    Title.BorderSizePixel = 0
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.Font = Enum.Font.GothamBold
    Title.Text = title or "Solara Library"
    Title.TextColor3 = theme.Text
    Title.TextSize = 20

    MinimizeButton.Name = "MinimizeButton"
    MinimizeButton.Parent = Title
    MinimizeButton.BackgroundColor3 = theme.Accent
    MinimizeButton.Position = UDim2.new(0.93, 0, 0, 5)
    MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
    MinimizeButton.Font = Enum.Font.GothamBold
    MinimizeButton.Text = "-"
    MinimizeButton.TextColor3 = theme.Text
    MinimizeButton.TextSize = 18

    MinimizeButton.MouseButton1Click:Connect(function()
        Toggle = not Toggle
        MainFrame:TweenSize(Toggle and UDim2.new(0, 500, 0, 40) or UDim2.new(0, 500, 0, 350), "Out", "Quad", 0.5, true)
    end)

    -- Introdução com animação
    local IntroFrame = Instance.new("Frame")
    IntroFrame.Name = "IntroFrame"
    IntroFrame.Parent = ScreenGui
    IntroFrame.BackgroundColor3 = theme.Secondary
    IntroFrame.BorderSizePixel = 0
    IntroFrame.Size = UDim2.new(1, 0, 1, 0)

    local IntroText = Instance.new("TextLabel")
    IntroText.Name = "IntroText"
    IntroText.Parent = IntroFrame
    IntroText.BackgroundTransparency = 1
    IntroText.Size = UDim2.new(1, 0, 1, 0)
    IntroText.Font = Enum.Font.GothamBlack
    IntroText.Text = "Solara Library"
    IntroText.TextColor3 = theme.Accent
    IntroText.TextSize = 60
    IntroText.TextStrokeTransparency = 0
    IntroText.TextTransparency = 1

    wait(1)
    IntroText:TweenTextTransparency(0, "Out", "Quad", 1.5, true)
    wait(2)
    IntroFrame:TweenSize(UDim2.new(1, 0, 0, 0), "Out", "Quad", 1.5, true, function()
        IntroFrame:Destroy()
    end)

    return MainFrame
end

function SolaraLibrary:CreateButton(parent, text, callback)
    local Button = Instance.new("TextButton")
    Button.Parent = parent
    Button.BackgroundColor3 = theme.Secondary
    Button.Size = UDim2.new(0, 200, 0, 45)
    Button.Font = Enum.Font.Gotham
    Button.Text = text or "Button"
    Button.TextColor3 = theme.Text
    Button.TextSize = 16
    Button.BorderSizePixel = 0
    Button.AutoButtonColor = false

    Button.MouseEnter:Connect(function()
        Button:TweenSize(UDim2.new(0, 210, 0, 50), "Out", "Quad", 0.2, true)
    end)

    Button.MouseLeave:Connect(function()
        Button:TweenSize(UDim2.new(0, 200, 0, 45), "Out", "Quad", 0.2, true)
    end)

    Button.MouseButton1Click:Connect(function()
        if callback then
            callback()
        end
    end)
end

function SolaraLibrary:CreateSlider(parent, min, max, callback)
    -- Slider com um visual elegante e animação
    local SliderFrame = Instance.new("Frame")
    local SliderButton = Instance.new("TextButton")
    local SliderLabel = Instance.new("TextLabel")

    SliderFrame.Parent = parent
    SliderFrame.BackgroundColor3 = theme.Secondary
    SliderFrame.Size = UDim2.new(0, 200, 0, 50)

    SliderButton.Parent = SliderFrame
    SliderButton.BackgroundColor3 = theme.Accent
    SliderButton.Size = UDim2.new(0, 20, 0, 50)

    SliderLabel.Parent = SliderFrame
    SliderLabel.BackgroundTransparency = 1
    SliderLabel.Position = UDim2.new(0.5, 0, 0, 0)
    SliderLabel.Size = UDim2.new(0.5, -10, 1, 0)
    SliderLabel.Font = Enum.Font.GothamBold
    SliderLabel.Text = tostring(min)
    SliderLabel.TextColor3 = theme.Text
    SliderLabel.TextSize = 14
    SliderLabel.TextXAlignment = Enum.TextXAlignment.Right

    SliderButton.MouseButton1Down:Connect(function()
        local moveConnection = UserInputService.InputChanged:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseMovement then
                local scale = math.clamp((input.Position.X - SliderFrame.AbsolutePosition.X) / SliderFrame.AbsoluteSize.X, 0, 1)
                SliderButton.Position = UDim2.new(scale, -10, 0, 0)
                local value = min + math.floor((max - min) * scale)
                SliderLabel.Text = tostring(value)
                if callback then
                    callback(value)
                end
            end
        end)

        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                moveConnection:Disconnect()
            end
        end)
    end)
end

function SolaraLibrary:CreateDropdown(parent, options, callback)
    local DropdownFrame = Instance.new("Frame")
    DropdownFrame.Parent = parent
    DropdownFrame.BackgroundColor3 = theme.Secondary
    DropdownFrame.Size = UDim2.new(0, 200, 0, 45)

    local DropdownButton = Instance.new("TextButton")
    DropdownButton.Parent = DropdownFrame
    DropdownButton.BackgroundColor3 = theme.Secondary
    DropdownButton.Size = UDim2.new(1, 0, 1, 0)
    DropdownButton.Font = Enum.Font.Gotham
    DropdownButton.Text = "Select an option"
    DropdownButton.TextColor3 = theme.Text
    DropdownButton.TextSize = 16
    DropdownButton.BorderSizePixel = 0

    local DropdownList = Instance.new("Frame")
    DropdownList.Parent = DropdownFrame
    DropdownList.Position = UDim2.new(0, 0, 1, 0)
    DropdownList.Size = UDim2.new(1, 0, 0, #options * 30)
    DropdownList.BackgroundColor3 = theme.Secondary
    DropdownList.Visible = false

    local UIListLayout = Instance.new("UIListLayout")
    UIListLayout.Parent = DropdownList

    DropdownButton.MouseButton1Click:Connect(function()
        DropdownList.Visible = not DropdownList.Visible
    end)

    for _, option in ipairs(options) do
        local OptionButton = Instance.new("TextButton")
        OptionButton.Parent = DropdownList
        OptionButton.Size = UDim2.new(1, 0, 0, 30)
        OptionButton.BackgroundColor3 = theme.Secondary
        OptionButton.Font = Enum.Font.Gotham
        OptionButton.Text = option
        OptionButton.TextColor3 = theme.Text
        OptionButton.TextSize = 14
        OptionButton.BorderSizePixel = 0

        OptionButton.MouseButton1Click:Connect(function()
            DropdownButton.Text = option
            DropdownList.Visible = false
            if callback then
                callback(option)
            end
        end)
    end
end

