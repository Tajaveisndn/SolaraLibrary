local Utils = loadstring(game:HttpGet("https://raw.githubusercontent.com/Tajaveisndn/Solara-Library/refs/heads/main/README.md"))()
local Config = Utils.Config
local Utility = Utils.Utility

-- Interface Principal
local Library = {}
local Window = {}
Window.__index = Window

function Library.new(title)
    -- Remove GUI anterior se existir
    if game:GetService("CoreGui"):FindFirstChild(Config.Name) then
        game:GetService("CoreGui"):FindFirstChild(Config.Name):Destroy()
    end
    
    -- GUI Principal
    local GUI = Utility.Create("ScreenGui", {
        Name = Config.Name,
        Parent = game:GetService("CoreGui"),
        ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    })
    
    -- Container Principal
    local Main = Utility.Create("Frame", {
        Name = "Main",
        BackgroundColor3 = Config.Theme.Background,
        BorderSizePixel = 0,
        Position = UDim2.new(0.5, -300, 0.5, -175),
        Size = UDim2.new(0, 600, 0, 350),
        Parent = GUI,
        ZIndex = 2
    })
    
    -- Adicionar visual
    Utility.AddCorner(Main)
    Utility.AddShadow(Main)
    
    -- Barra Superior
    local TopBar = Utility.Create("Frame", {
        Name = "TopBar",
        BackgroundColor3 = Config.Theme.Primary,
        Size = UDim2.new(1, 0, 0, 35),
        Parent = Main,
        ZIndex = 3
    })
    
    Utility.AddCorner(TopBar)
    Utility.AddGradient(TopBar)
    
    -- Título
    local Title = Utility.Create("TextLabel", {
        Name = "Title",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 12, 0, 0),
        Size = UDim2.new(1, -70, 1, 0),
        Font = Config.Font,
        Text = title or Config.Name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize + 2,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = TopBar,
        ZIndex = 3
    })
    
    -- Botões de Controle
    local CloseButton = Utility.Create("ImageButton", {
        Name = "CloseButton",
        BackgroundTransparency = 1,
        Position = UDim2.new(1, -30, 0.5, -8),
        Size = UDim2.new(0, 16, 0, 16),
        Image = "rbxassetid://9127564477",
        ImageColor3 = Config.Theme.Text,
        Parent = TopBar,
        ZIndex = 3
    })
    
    local MinimizeButton = Utility.Create("ImageButton", {
        Name = "MinimizeButton",
        BackgroundTransparency = 1,
        Position = UDim2.new(1, -55, 0.5, -8),
        Size = UDim2.new(0, 16, 0, 16),
        Image = "rbxassetid://9127563210",
        ImageColor3 = Config.Theme.Text,
        Parent = TopBar,
        ZIndex = 3
    })
    
    -- Container das Tabs
    local TabContainer = Utility.Create("Frame", {
        Name = "TabContainer",
        BackgroundColor3 = Config.Theme.DarkBG,
        Position = UDim2.new(0, 8, 0, 43),
        Size = UDim2.new(0, 130, 1, -51),
        Parent = Main,
        ZIndex = 2
    })
    
    Utility.AddCorner(TabContainer)
    
    local TabList = Utility.Create("ScrollingFrame", {
        Name = "TabList",
        Active = true,
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 0, 0, 0),
        Size = UDim2.new(1, 0, 1, 0),
        ScrollBarThickness = 0,
        Parent = TabContainer,
        ZIndex = 2
    })
    
    local TabListLayout = Utility.Create("UIListLayout", {
        Padding = UDim.new(0, 5),
        SortOrder = Enum.SortOrder.LayoutOrder,
        Parent = TabList
    })
    
    Utility.AddPadding(TabList, 8)
    
    -- Container do Conteúdo
    local ContentContainer = Utility.Create("Frame", {
        Name = "ContentContainer",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 146, 0, 43),
        Size = UDim2.new(1, -154, 1, -51),
        Parent = Main,
        ZIndex = 2
    })
    
    -- Sistema de Minimizar
    local minimized = false
    local originalSize = Main.Size
    
    MinimizeButton.MouseButton1Click:Connect(function()
        minimized = not minimized
        
        if minimized then
            Utility.Tween(Main, {Size = UDim2.new(0, 600, 0, 35)})
            Utility.Tween(TabContainer, {BackgroundTransparency = 1})
            for _, child in pairs(ContentContainer:GetChildren()) do
                child.Visible = false
            end
        else
            Utility.Tween(Main, {Size = originalSize})
            Utility.Tween(TabContainer, {BackgroundTransparency = 0})
            if Window.CurrentTab then
                Window.CurrentTab.Content.Visible = true
            end
        end
    end)
    
    -- Fechar GUI
    CloseButton.MouseButton1Click:Connect(function()
        Utility.Tween(GUI, {BackgroundTransparency = 1}, 0.2)
        wait(0.2)
        GUI:Destroy()
    end)
    
    -- Sistema de Arrasto
    local dragging, dragInput, dragStart, startPos
    
    local function updateDrag(input)
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
    
    TopBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = Main.Position
        end
    end)
    
    UIS.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            updateDrag(input)
        end
    end)
    
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    -- Sistema de Tabs
    function Window:CreateTab(name)
        local Tab = {}
        
        -- Botão da Tab
        local TabButton = Utility.Create("TextButton", {
            Name = name.."Tab",
            BackgroundColor3 = Config.Theme.Primary,
            BackgroundTransparency = 0.9,
            Size = UDim2.new(1, 0, 0, 32),
            Font = Config.Font,
            Text = name,
            TextColor3 = Config.Theme.Text,
            TextSize = Config.TextSize,
            Parent = TabList,
            ZIndex = 2
        })
        
        Utility.AddCorner(TabButton)
        
        -- Indicador da Tab
        local TabIndicator = Utility.Create("Frame", {
            Name = "Indicator",
            BackgroundColor3 = Config.Theme.Primary,
            Position = UDim2.new(0, 4, 0.5, -4),
            Size = UDim2.new(0, 0, 0, 8),
            Parent = TabButton,
            ZIndex = 2
        })
        
        Utility.AddCorner(TabIndicator, 4)
        
        -- Conteúdo da Tab
        local TabContent = Utility.Create("ScrollingFrame", {
            Name = name.."Content",
            BackgroundTransparency = 1,
            ClipsDescendants = true,
            Size = UDim2.new(1, 0, 1, 0),
            CanvasSize = UDim2.new(0, 0, 0, 0),
            ScrollBarThickness = 3,
            ScrollBarImageColor3 = Config.Theme.Primary,
            Visible = false,
            Parent = ContentContainer,
            ZIndex = 2
        })
        
        local ContentLayout = Utility.Create("UIListLayout", {
            Padding = UDim.new(0, 6),
            SortOrder = Enum.SortOrder.LayoutOrder,
            Parent = TabContent
        })
        
        Utility.AddPadding(TabContent, 8)
        
        -- Atualizar CanvasSize automaticamente
        ContentLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            TabContent.CanvasSize = UDim2.new(0, 0, 0, ContentLayout.AbsoluteContentSize.Y + 16)
        end)
        
        -- Lógica de seleção da Tab
        TabButton.MouseButton1Click:Connect(function()
            if Window.CurrentTab then
                -- Desativar tab atual
                Utility.Tween(Window.CurrentTab.Indicator, {Size = UDim2.new(0, 0, 0, 8)})
                Window.CurrentTab.Button.BackgroundTransparency = 0.9
                Window.CurrentTab.Content.Visible = false
            end
            
            -- Ativar nova tab
            Window.CurrentTab = {
                Button = TabButton,
                Indicator = TabIndicator,
                Content = TabContent
            }
            
            Utility.Tween(TabIndicator, {Size = UDim2.new(0, 4, 0, 8)})
            Utility.Tween(TabButton, {BackgroundTransparency = 0.7})
            if not minimized then
                TabContent.Visible = true
            end
        end)
        
        return Tab
    end
    
    -- Atalho para minimizar (Ctrl + M)
    Utility.KeyPressed(Enum.KeyCode.M, function()
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
            MinimizeButton.MouseButton1Click:Fire()
        end
    end)
    
    return Window
end

return Library

-- Elementos da Interface
local Elements = {}

-- Função para criar containers de elementos
local function CreateElementContainer(parent, size)
    local container = Utility.Create("Frame", {
        Name = "ElementContainer",
        BackgroundColor3 = Config.Theme.LightBG,
        Size = UDim2.new(1, 0, 0, size or Config.ElementHeight),
        Parent = parent,
        ZIndex = 3
    })
    
    Utility.AddCorner(container)
    return container
end

-- Toggle
function Elements:AddToggle(tab, name, default, callback)
    callback = callback or function() end
    local toggled = default or false
    
    local Container = CreateElementContainer(tab, 36)
    
    -- Label
    local Label = Utility.Create("TextLabel", {
        Name = "Label",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0, 0),
        Size = UDim2.new(1, -60, 1, 0),
        Font = Config.Font,
        Text = name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = Container,
        ZIndex = 4
    })
    
    -- Toggle Button
    local ToggleButton = Utility.Create("Frame", {
        Name = "ToggleButton",
        BackgroundColor3 = toggled and Config.Theme.Primary or Config.Theme.Border,
        Position = UDim2.new(1, -46, 0.5, -10),
        Size = UDim2.new(0, 36, 0, 20),
        Parent = Container,
        ZIndex = 4
    })
    
    Utility.AddCorner(ToggleButton, 10)
    
    -- Toggle Circle
    local Circle = Utility.Create("Frame", {
        Name = "Circle",
        BackgroundColor3 = Config.Theme.Text,
        Position = toggled and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8),
        Size = UDim2.new(0, 16, 0, 16),
        Parent = ToggleButton,
        ZIndex = 4
    })
    
    Utility.AddCorner(Circle, 8)
    
    -- Click Handler
    local debounce = false
    ToggleButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 and not debounce then
            debounce = true
            toggled = not toggled
            
            -- Animação
            Utility.Tween(Circle, {
                Position = toggled and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
            })
            
            Utility.Tween(ToggleButton, {
                BackgroundColor3 = toggled and Config.Theme.Primary or Config.Theme.Border
            })
            
            callback(toggled)
            wait(0.2)
            debounce = false
        end
    end)
    
    -- Hover Effect
    Utility.AddHover(Container, Config.Theme.DarkBG, Config.Theme.LightBG)
end

-- Slider
function Elements:AddSlider(tab, name, min, max, default, precision, callback)
    callback = callback or function() end
    local value = default or min
    precision = precision or 0
    
    local Container = CreateElementContainer(tab, 50)
    
    -- Label
    local Label = Utility.Create("TextLabel", {
        Name = "Label",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0, 0),
        Size = UDim2.new(1, -20, 0, 20),
        Font = Config.Font,
        Text = name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = Container,
        ZIndex = 4
    })
    
    -- Slider Bar
    local SliderBar = Utility.Create("Frame", {
        Name = "SliderBar",
        BackgroundColor3 = Config.Theme.Border,
        Position = UDim2.new(0, 10, 0, 32),
        Size = UDim2.new(1, -90, 0, 4),
        Parent = Container,
        ZIndex = 4
    })
    
    Utility.AddCorner(SliderBar, 2)
    
    -- Slider Fill
    local SliderFill = Utility.Create("Frame", {
        Name = "SliderFill",
        BackgroundColor3 = Config.Theme.Primary,
        Size = UDim2.new((value - min)/(max - min), 0, 1, 0),
        Parent = SliderBar,
        ZIndex = 4
    })
    
    Utility.AddCorner(SliderFill, 2)
    
    -- Value Box
    local ValueBox = Utility.Create("TextBox", {
        Name = "ValueBox",
        BackgroundColor3 = Config.Theme.DarkBG,
        Position = UDim2.new(1, -70, 0, 25),
        Size = UDim2.new(0, 60, 0, 20),
        Font = Config.Font,
        Text = tostring(value),
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        Parent = Container,
        ZIndex = 4
    })
    
    Utility.AddCorner(ValueBox)
    
    -- Funções do Slider
    local function UpdateSlider(input)
        local pos = UDim2.new(math.clamp((input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1), 0, 1, 0)
        SliderFill.Size = pos
        
        local newValue = min + (max - min) * pos.X.Scale
        value = math.floor(newValue * (10 ^ precision)) / (10 ^ precision)
        ValueBox.Text = tostring(value)
        
        callback(value)
    end
    
    -- Input Handlers
    local sliding = false
    
    SliderBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            sliding = true
            UpdateSlider(input)
        end
    end)
    
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            sliding = false
        end
    end)
    
    UIS.InputChanged:Connect(function(input)
        if sliding and input.UserInputType == Enum.UserInputType.MouseMovement then
            UpdateSlider(input)
        end
    end)
    
    -- Value Box Handler
    ValueBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local newValue = tonumber(ValueBox.Text)
            if newValue then
                value = math.clamp(newValue, min, max)
                value = math.floor(value * (10 ^ precision)) / (10 ^ precision)
                ValueBox.Text = tostring(value)
                SliderFill.Size = UDim2.new((value - min)/(max - min), 0, 1, 0)
                callback(value)
            else
                ValueBox.Text = tostring(value)
            end
        end
    end)
    
    -- Hover Effect
    Utility.AddHover(Container, Config.Theme.DarkBG, Config.Theme.LightBG)
end

-- Button
function Elements:AddButton(tab, name, callback)
    callback = callback or function() end
    
    local Container = CreateElementContainer(tab, 36)
    
    -- Button
    local Button = Utility.Create("TextButton", {
        Name = "Button",
        BackgroundColor3 = Config.Theme.Primary,
        Size = UDim2.new(1, 0, 1, 0),
        Font = Config.Font,
        Text = name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        Parent = Container,
        ZIndex = 4
    })
    
    Utility.AddCorner(Button)
    Utility.AddGradient(Button)
    
    -- Click Handler
    local debounce = false
    Button.MouseButton1Click:Connect(function()
        if not debounce then
            debounce = true
            
            -- Efeito de clique
            Utility.Tween(Button, {BackgroundTransparency = 0.4}, 0.1)
            wait(0.1)
            Utility.Tween(Button, {BackgroundTransparency = 0}, 0.1)
            
            callback()
            wait(0.2)
            debounce = false
        end
    end)
    
    -- Hover Effect
    Utility.AddHover(Button, Config.Theme.Secondary, Config.Theme.Primary)
end

-- Label
function Elements:AddLabel(tab, text)
    local Container = CreateElementContainer(tab, 30)
    
    local Label = Utility.Create("TextLabel", {
        Name = "Label",
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 1, 0),
        Font = Config.Font,
        Text = text,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        Parent = Container,
        ZIndex = 4
    })
end

return Elements

-- Elementos Avançados
local AdvancedElements = {}

-- Dropdown
function AdvancedElements:AddDropdown(tab, name, options, default, callback)
    callback = callback or function() end
    options = options or {}
    
    local Container = CreateElementContainer(tab, 36)
    local Extended = false
    local CurrentOption = default or "Selecione..."
    
    -- Label
    local Label = Utility.Create("TextLabel", {
        Name = "Label",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0, 0),
        Size = UDim2.new(1, -20, 0, 16),
        Font = Config.Font,
        Text = name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = Container,
        ZIndex = 4
    })
    
    -- Selected Value
    local SelectedValue = Utility.Create("TextLabel", {
        Name = "SelectedValue",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0, 20),
        Size = UDim2.new(1, -40, 0, 14),
        Font = Config.Font,
        Text = CurrentOption,
        TextColor3 = Config.Theme.DarkText,
        TextSize = Config.TextSize - 1,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = Container,
        ZIndex = 4
    })
    
    -- Arrow
    local Arrow = Utility.Create("ImageLabel", {
        Name = "Arrow",
        BackgroundTransparency = 1,
        Position = UDim2.new(1, -26, 0.5, -6),
        Size = UDim2.new(0, 12, 0, 12),
        Image = "rbxassetid://6034818379",
        ImageColor3 = Config.Theme.DarkText,
        Parent = Container,
        ZIndex = 4
    })
    
    -- Options Container
    local OptionsContainer = Utility.Create("Frame", {
        Name = "OptionsContainer",
        BackgroundColor3 = Config.Theme.DarkBG,
        Position = UDim2.new(0, 0, 1, 5),
        Size = UDim2.new(1, 0, 0, 0),
        ClipsDescendants = true,
        Visible = false,
        Parent = Container,
        ZIndex = 5
    })
    
    Utility.AddCorner(OptionsContainer)
    
    -- Search Box
    local SearchBox = Utility.Create("TextBox", {
        Name = "SearchBox",
        BackgroundColor3 = Config.Theme.LightBG,
        Position = UDim2.new(0, 5, 0, 5),
        Size = UDim2.new(1, -10, 0, 24),
        Font = Config.Font,
        PlaceholderText = "Pesquisar...",
        Text = "",
        TextColor3 = Config.Theme.Text,
        PlaceholderColor3 = Config.Theme.DarkText,
        TextSize = Config.TextSize,
        Parent = OptionsContainer,
        ZIndex = 6
    })
    
    Utility.AddCorner(SearchBox)
    
    -- Options List
    local OptionsList = Utility.Create("ScrollingFrame", {
        Name = "OptionsList",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 5, 0, 34),
        Size = UDim2.new(1, -10, 1, -39),
        CanvasSize = UDim2.new(0, 0, 0, 0),
        ScrollBarThickness = 3,
        ScrollBarImageColor3 = Config.Theme.Primary,
        Parent = OptionsContainer,
        ZIndex = 6
    })
    
    local OptionsLayout = Utility.Create("UIListLayout", {
        Padding = UDim.new(0, 4),
        Parent = OptionsList
    })
    
    -- Funções do Dropdown
    local function CreateOption(text)
        local Option = Utility.Create("TextButton", {
            Name = text.."Option",
            BackgroundColor3 = Config.Theme.LightBG,
            Size = UDim2.new(1, -2, 0, 28),
            Font = Config.Font,
            Text = text,
            TextColor3 = Config.Theme.Text,
            TextSize = Config.TextSize,
            Parent = OptionsList,
            ZIndex = 6
        })
        
        Utility.AddCorner(Option)
        
        -- Hover Effect
        Utility.AddHover(Option, Config.Theme.Primary, Config.Theme.LightBG)
        
        Option.MouseButton1Click:Connect(function()
            CurrentOption = text
            SelectedValue.Text = text
            callback(text)
            
            Extended = false
            Utility.Tween(OptionsContainer, {Size = UDim2.new(1, 0, 0, 0)}, 0.2)
            Utility.Tween(Arrow, {Rotation = 0}, 0.2)
            wait(0.2)
            OptionsContainer.Visible = false
        end)
        
        return Option
    end
    
    -- Adicionar opções iniciais
    for _, option in ipairs(options) do
        CreateOption(option)
    end
    
    -- Atualizar tamanho da lista
    OptionsLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        OptionsList.CanvasSize = UDim2.new(0, 0, 0, OptionsLayout.AbsoluteContentSize.Y + 4)
    end)
    
    -- Search Handler
    SearchBox:GetPropertyChangedSignal("Text"):Connect(function()
        local search = string.lower(SearchBox.Text)
        for _, option in ipairs(OptionsList:GetChildren()) do
            if option:IsA("TextButton") then
                if search == "" then
                    option.Visible = true
                else
                    option.Visible = string.find(string.lower(option.Text), search) ~= nil
                end
            end
        end
    end)
    
    -- Toggle Dropdown
    Container.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Extended = not Extended
            
            if Extended then
                OptionsContainer.Visible = true
                Utility.Tween(OptionsContainer, {Size = UDim2.new(1, 0, 0, math.min(#options * 32 + 38, 200))}, 0.2)
                Utility.Tween(Arrow, {Rotation = 180}, 0.2)
                SearchBox:CaptureFocus()
            else
                Utility.Tween(OptionsContainer, {Size = UDim2.new(1, 0, 0, 0)}, 0.2)
                Utility.Tween(Arrow, {Rotation = 0}, 0.2)
                wait(0.2)
                OptionsContainer.Visible = false
            end
        end
    end)
end

-- ColorPicker
function AdvancedElements:AddColorPicker(tab, name, default, callback)
    callback = callback or function() end
    default = default or Color3.fromRGB(255, 255, 255)
    
    local Container = CreateElementContainer(tab, 36)
    local Extended = false
    
    -- Label
    local Label = Utility.Create("TextLabel", {
        Name = "Label",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0, 0),
        Size = UDim2.new(1, -60, 1, 0),
        Font = Config.Font,
        Text = name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = Container,
        ZIndex = 4
    })
    
    -- Color Display
    local ColorDisplay = Utility.Create("Frame", {
        Name = "ColorDisplay",
        BackgroundColor3 = default,
        Position = UDim2.new(1, -46, 0.5, -10),
        Size = UDim2.new(0, 36, 0, 20),
        Parent = Container,
        ZIndex = 4
    })
    
    Utility.AddCorner(ColorDisplay)
    
    -- Color Picker Container
    local PickerContainer = Utility.Create("Frame", {
        Name = "PickerContainer",
        BackgroundColor3 = Config.Theme.DarkBG,
        Position = UDim2.new(0, 0, 1, 5),
        Size = UDim2.new(1, 0, 0, 0),
        ClipsDescendants = true,
        Visible = false,
        Parent = Container,
        ZIndex = 5
    })
    
    Utility.AddCorner(PickerContainer)
    
    -- Color Saturation
    local Saturation = Utility.Create("ImageLabel", {
        Name = "Saturation",
        BackgroundColor3 = default,
        Position = UDim2.new(0, 5, 0, 5),
        Size = UDim2.new(1, -10, 0, 150),
        Image = "rbxassetid://4155801252",
        Parent = PickerContainer,
        ZIndex = 6
    })
    
    Utility.AddCorner(Saturation)
    
    local SaturationPicker = Utility.Create("Frame", {
        Name = "SaturationPicker",
        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
        Position = UDim2.new(0, 0, 0, 0),
        Size = UDim2.new(0, 4, 0, 4),
        Parent = Saturation,
        ZIndex = 7
    })
    
    Utility.AddCorner(SaturationPicker, 4)
    
    -- RGB Inputs
    local function CreateColorInput(name, position)
        local InputContainer = Utility.Create("Frame", {
            Name = name.."Input",
            BackgroundColor3 = Config.Theme.LightBG,
            Position = position,
            Size = UDim2.new(0, 70, 0, 24),
            Parent = PickerContainer,
            ZIndex = 6
        })
        
        Utility.AddCorner(InputContainer)
        
        local InputLabel = Utility.Create("TextLabel", {
            Name = "Label",
            BackgroundTransparency = 1,
            Position = UDim2.new(0, 6, 0, 0),
            Size = UDim2.new(0, 20, 1, 0),
            Font = Config.Font,
            Text = name,
            TextColor3 = Config.Theme.DarkText,
            TextSize = Config.TextSize - 2,
            Parent = InputContainer,
            ZIndex = 7
        })
        
        local Input = Utility.Create("TextBox", {
            Name = "Input",
            BackgroundTransparency = 1,
            Position = UDim2.new(0, 26, 0, 0),
            Size = UDim2.new(1, -32, 1, 0),
            Font = Config.Font,
            Text = "255",
            TextColor3 = Config.Theme.Text,
            TextSize = Config.TextSize - 2,
            Parent = InputContainer,
            ZIndex = 7
        })
        
        return Input
    end
    
    local RInput = CreateColorInput("R", UDim2.new(0, 5, 0, 160))
    local GInput = CreateColorInput("G", UDim2.new(0.5, -35, 0, 160))
    local BInput = CreateColorInput("B", UDim2.new(1, -75, 0, 160))
    
    -- Update Functions
    local function UpdateColor(color)
        ColorDisplay.BackgroundColor3 = color
        Saturation.BackgroundColor3 = color
        callback(color)
    end
    
    local function UpdateFromRGB()
        local r = tonumber(RInput.Text) or 255
        local g = tonumber(GInput.Text) or 255
        local b = tonumber(BInput.Text) or 255
        
        r = math.clamp(r, 0, 255)
        g = math.clamp(g, 0, 255)
        b = math.clamp(b, 0, 255)
        
        UpdateColor(Color3.fromRGB(r, g, b))
    end
    
    -- Input Handlers
    RInput.FocusLost:Connect(UpdateFromRGB)
    GInput.FocusLost:Connect(UpdateFromRGB)
    BInput.FocusLost:Connect(UpdateFromRGB)
    
    -- Saturation Handler
    local draggingSaturation = false
    
    Saturation.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingSaturation = true
        end
    end)
    
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingSaturation = false
        end
    end)
    
    UIS.InputChanged:Connect(function(input)
        if draggingSaturation and input.UserInputType == Enum.UserInputType.MouseMovement then
            local saturationX = math.clamp((input.Position.X - Saturation.AbsolutePosition.X) / Saturation.AbsoluteSize.X, 0, 1)
            local saturationY = math.clamp((input.Position.Y - Saturation.AbsolutePosition.Y) / Saturation.AbsoluteSize.Y, 0, 1)
            
            SaturationPicker.Position = UDim2.new(saturationX, -2, saturationY, -2)
            
            local color = Color3.fromHSV(saturationX, 1 - saturationY, 1)
            UpdateColor(color)
            
            RInput.Text = tostring(math.floor(color.R * 255))
            GInput.Text = tostring(math.floor(color.G * 255))
            BInput.Text = tostring(math.floor(color.B * 255))
        end
    end)
    
    -- Toggle ColorPicker
    ColorDisplay.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Extended = not Extended
            
            if Extended then
                PickerContainer.Visible = true
                Utility.Tween(PickerContainer, {Size = UDim2.new(1, 0, 0, 190)}, 0.2)
            else
                Utility.Tween(PickerContainer, {Size = UDim2.new(1, 0, 0, 0)}, 0.2)
                wait(0.2)
                PickerContainer.Visible = false
            end
        end
    end)
end

-- KeyBind
function AdvancedElements:AddKeybind(tab, name, default, callback)
    -- KeyBind (continuação)
    callback = callback or function() end
    local Container = CreateElementContainer(tab, 36)
    local CurrentKey = default or Enum.KeyCode.Unknown
    local Binding = false
    
    -- Label
    local Label = Utility.Create("TextLabel", {
        Name = "Label",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0, 0),
        Size = UDim2.new(1, -90, 1, 0),
        Font = Config.Font,
        Text = name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = Container,
        ZIndex = 4
    })
    
    -- Bind Button
    local BindButton = Utility.Create("TextButton", {
        Name = "BindButton",
        BackgroundColor3 = Config.Theme.DarkBG,
        Position = UDim2.new(1, -80, 0.5, -12),
        Size = UDim2.new(0, 70, 0, 24),
        Font = Config.Font,
        Text = CurrentKey.Name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize - 1,
        Parent = Container,
        ZIndex = 4
    })
    
    Utility.AddCorner(BindButton)
    
    -- Bind Handler
    BindButton.MouseButton1Click:Connect(function()
        if not Binding then
            Binding = true
            BindButton.Text = "..."
            
            local connection
            connection = UIS.InputBegan:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.Keyboard then
                    CurrentKey = input.KeyCode
                    BindButton.Text = CurrentKey.Name
                    Binding = false
                    connection:Disconnect()
                end
            end)
        end
    end)
    
    -- Keybind Listener
    UIS.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode == CurrentKey and not Binding then
            callback(CurrentKey)
        end
    end)
    
    -- Hover Effect
    Utility.AddHover(BindButton, Config.Theme.LightBG, Config.Theme.DarkBG)
end

-- TextBox
function AdvancedElements:AddTextBox(tab, name, placeholder, callback)
    callback = callback or function() end
    local Container = CreateElementContainer(tab, 50)
    
    -- Label
    local Label = Utility.Create("TextLabel", {
        Name = "Label",
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 10, 0, 0),
        Size = UDim2.new(1, -20, 0, 20),
        Font = Config.Font,
        Text = name,
        TextColor3 = Config.Theme.Text,
        TextSize = Config.TextSize,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = Container,
        ZIndex = 4
    })
    
    -- TextBox
    local Input = Utility.Create("TextBox", {
        Name = "Input",
        BackgroundColor3 = Config.Theme.DarkBG,
        Position = UDim2.new(0, 10, 0, 25),
        Size = UDim2.new(1, -20, 0, 20),
        Font = Config.Font,
        PlaceholderText = placeholder or "Digite aqui...",
        Text = "",
        TextColor3 = Config.Theme.Text,
        PlaceholderColor3 = Config.Theme.DarkText,
        TextSize = Config.TextSize - 1,
        Parent = Container,
        ZIndex = 4
    })
    
    Utility.AddCorner(Input)
    
    -- Input Handler
    Input.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            callback(Input.Text)
        end
    end)
    
    -- Hover Effect
    Utility.AddHover(Input, Config.Theme.LightBG, Config.Theme.DarkBG)
end

return AdvancedElements
