-- =============================================================================
--    HERMANOS CP // HUB LOADER (OWNER ALWAYS FIXED & UPDATED) - PARTE 1
-- =============================================================================

local Players = game:GetService("Players")
local CoreGuiService = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

-- FUNCIÓN AUXILIAR: Convierte una fecha del mundo real a Segundos Unix de forma independiente
local function crearFechaLimite(year, month, day, hour, min)
    return os.time({year = year, month = month, day = day, hour = hour or 0, min = min or 0, sec = 0})
end

-- =============================================================================
-- BASE DE DATOS REAL E INDEPENDIENTE
-- =============================================================================
local Licencias = {
    [10908318368] = {fechaLimite = "Infinito", diasIniciales = "Infinito", rol = "OWNER"},
    [4252198913] = {fechaLimite = "Infinito", diasIniciales = "Infinito", rol = "OWNER"},
    [4187247061] = {fechaLimite = crearFechaLimite(2026, 7, 23, 10, 38), diasIniciales = 3, rol = "PRUEBA GRATIS"},
    [649895271] = {fechaLimite = crearFechaLimite(2026, 8, 21, 23, 59), diasIniciales = 30, rol = "USUARIO"},
    [8744036907] = {fechaLimite = crearFechaLimite(2026, 8, 15, 23, 59), diasIniciales = 25, rol = "USUARIO"},
    [5510998492] = {fechaLimite = crearFechaLimite(2026, 8, 13, 23, 59), diasIniciales = 23, rol = "USUARIO"},
    [9588424071] = {fechaLimite = crearFechaLimite(2026, 8, 14, 23, 59), diasIniciales = 24, rol = "USUARIO"},
    [9571972521] = {fechaLimite = crearFechaLimite(2026, 8, 4, 23, 59), diasIniciales = 14, rol = "USUARIO"},
    [10908320401] = {fechaLimite = crearFechaLimite(2026, 8, 14, 23, 59), diasIniciales = 24, rol = "USUARIO"},
    [2313141924] = {fechaLimite = crearFechaLimite(2026, 7, 26, 23, 59), diasIniciales = 5, rol = "USUARIO"},
    [2415561227] = {fechaLimite = crearFechaLimite(2026, 9, 5, 23, 59), diasIniciales = 45, rol = "USUARIO"},
    [9663173706] = {fechaLimite = crearFechaLimite(2026, 7, 23, 23, 59), diasIniciales = 1, rol = "USUARIO"}
}

local jugadorId = LocalPlayer.UserId
local datosLicencia = Licencias[jugadorId]

-- VERIFICACIÓN DE SEGURIDAD INICIAL
if not datosLicencia then
    LocalPlayer:Kick("No tienes una licencia registrada para Hermanos CP. Contacta al administrador.")
    return
end

-- CONTROLADOR INDEPENDIENTE: Bucle que verifica si el tiempo del mundo real ya expiró
task.spawn(function()
    while true do
        task.wait(1)
        local tiempoActual = os.time()
        
        if typeof(datosLicencia.fechaLimite) == "number" then
            if tiempoActual >= datosLicencia.fechaLimite then
                LocalPlayer:Kick("Tu licencia de Hermanos CP ha expirado. Renueva tus días en el Discord.")
                break
            end
        end
    end
end)

-- AUXILIAR: Calcula la diferencia real en segundos y la formatea
local function obtenerTiempoRestanteTexto(fechaLimite)
    if fechaLimite == "Infinito" then return "Infinito" end
    local segundosRestantes = fechaLimite - os.time()
    
    if segundosRestantes <= 0 then return "Expirado" end
    
    local dias = math.floor(segundosRestantes / 86400)
    local horas = math.floor((segundosRestantes % 86400) / 3600)
    local minutos = math.floor((segundosRestantes % 3600) / 60)
    local segs = math.floor(segundosRestantes % 60)
    
    return string.format("%d d, %d h, %d m, %d s", dias, horas, minutos, segs)
end

-- RESOLUCIÓN DE PARENT SEGURO
local function getSafeGuiParent()
    local success, parent = pcall(function()
        return (type(cloneref) == "function" and cloneref(CoreGuiService)) or CoreGuiService
    end)
    if success and parent then return parent end
    return LocalPlayer:WaitForChild("PlayerGui", 5)
end

-- INTERFAZ PRINCIPAL DEL LOADER
local LoaderGui = Instance.new("ScreenGui")
LoaderGui.Name = "HermanosCPLoader_Premium"
LoaderGui.IgnoreGuiInset = true
LoaderGui.ResetOnSpawn = false
LoaderGui.Parent = getSafeGuiParent()

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 390, 0, 330)
MainFrame.Position = UDim2.new(0.5, -195, 0.5, -165)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = LoaderGui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 14)
mainCorner.Parent = MainFrame

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = Color3.fromRGB(0, 180, 255)
mainStroke.Thickness = 1.5
mainStroke.Transparency = 0.2
mainStroke.Parent = MainFrame

local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(15, 12, 28)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(8, 8, 12)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(5, 15, 20)),
})
gradient.Rotation = 45
gradient.Parent = MainFrame

-- ENCABEZADO
local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 48)
header.BackgroundColor3 = Color3.fromRGB(14, 14, 22)
header.BorderSizePixel = 0
header.Parent = MainFrame

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 14)
headerCorner.Parent = header

local headerBlock = Instance.new("Frame")
headerBlock.Size = UDim2.new(1, 0, 0, 10)
headerBlock.Position = UDim2.new(0, 0, 1, -10)
headerBlock.BackgroundColor3 = Color3.fromRGB(14, 14, 22)
headerBlock.BorderSizePixel = 0
headerBlock.Parent = header

local headerSeparator = Instance.new("Frame")
headerSeparator.Size = UDim2.new(1, 0, 0, 1)
headerSeparator.Position = UDim2.new(0, 0, 1, 0)
headerSeparator.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
headerSeparator.BorderSizePixel = 0
headerSeparator.Parent = header

local backBtn = Instance.new("TextButton")
backBtn.Size = UDim2.new(0, 26, 0, 26)
backBtn.Position = UDim2.new(0, 12, 0.5, -13)
backBtn.Text = "◀"
backBtn.TextSize = 12
backBtn.Font = Enum.Font.GothamBold
backBtn.TextColor3 = Color3.fromRGB(150, 160, 185)
backBtn.BackgroundTransparency = 1
backBtn.Visible = false
backBtn.Parent = header

local title = Instance.new("TextLabel")
title.Font = Enum.Font.Michroma
title.TextSize = 11
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Size = UDim2.new(1, -80, 1, 0)
title.Position = UDim2.new(0, 18, 0, 0)
title.BackgroundTransparency = 1
title.Text = "💎 HERMANOS CP"
title.RichText = true
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = header

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 26, 0, 26)
closeBtn.Position = UDim2.new(1, -38, 0.5, -13)
closeBtn.Text = "×"
closeBtn.TextSize = 18
closeBtn.Font = Enum.Font.GothamMedium
closeBtn.TextColor3 = Color3.fromRGB(255, 90, 110)
closeBtn.BackgroundColor3 = Color3.fromRGB(28, 16, 22)
closeBtn.BorderSizePixel = 0
closeBtn.Parent = header

local closeBtnCorner = Instance.new("UICorner")
closeBtnCorner.CornerRadius = UDim.new(0, 6)
closeBtnCorner.Parent = closeBtn

closeBtn.MouseButton1Click:Connect(function() LoaderGui:Destroy() end)

-- SUBTÍTULO
local subtitle = Instance.new("TextLabel")
subtitle.Font = Enum.Font.Ubuntu
subtitle.TextSize = 12
subtitle.TextColor3 = Color3.fromRGB(150, 160, 185)
subtitle.Size = UDim2.new(1, 0, 0, 20)
subtitle.Position = UDim2.new(0, 0, 0, 62)
subtitle.BackgroundTransparency = 1
subtitle.Text = "Elija la optimización para su sistema de juego:"
subtitle.Parent = MainFrame

-- CONTENEDOR DE BOTONES
local btnContainer = Instance.new("Frame")
btnContainer.Size = UDim2.new(1, -36, 0, 156)
btnContainer.Position = UDim2.new(0, 18, 0, 95)
btnContainer.BackgroundTransparency = 1
btnContainer.Parent = MainFrame

local layout = Instance.new("UIListLayout")
layout.FillDirection = Enum.FillDirection.Vertical
layout.Padding = UDim.new(0, 12)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = btnContainer

-- FUNCIÓN CREAR BOTONES
local function createLoaderButton(text, subText, color, order)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 68)
    btn.BackgroundColor3 = Color3.fromRGB(16, 16, 26)
    btn.BorderSizePixel = 0
    btn.Text = ""
    btn.LayoutOrder = order
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = btn
    
    local stroke = Instance.new("UIStroke")
    stroke.Thickness = 1.2
    stroke.Color = Color3.fromRGB(32, 32, 48)
    stroke.Transparency = 0.4
    stroke.Parent = btn

    local mainTxt = Instance.new("TextLabel")
    mainTxt.Font = Enum.Font.GothamBold
    mainTxt.TextSize = 13
    mainTxt.TextColor3 = Color3.fromRGB(245, 245, 250)
    mainTxt.Size = UDim2.new(1, -50, 0, 20)
    mainTxt.Position = UDim2.new(0, 16, 0, 8)
    mainTxt.TextXAlignment = Enum.TextXAlignment.Left
    mainTxt.BackgroundTransparency = 1
    mainTxt.Text = text
    mainTxt.Parent = btn

    local descTxt = Instance.new("TextLabel")
    descTxt.Font = Enum.Font.Ubuntu
    descTxt.TextSize = 11
    descTxt.TextColor3 = Color3.fromRGB(130, 140, 160)
    descTxt.Size = UDim2.new(1, -50, 0, 32)
    descTxt.Position = UDim2.new(0, 16, 0, 28)
    descTxt.TextXAlignment = Enum.TextXAlignment.Left
    descTxt.TextYAlignment = Enum.TextYAlignment.Top
    descTxt.BackgroundTransparency = 1
    descTxt.TextWrapped = true
    descTxt.Text = subText
    descTxt.Parent = btn
    
    local arrow = Instance.new("TextLabel")
    arrow.Font = Enum.Font.GothamBold
    arrow.TextSize = 14
    arrow.TextColor3 = Color3.fromRGB(60, 60, 80)
    arrow.Size = UDim2.new(0, 20, 1, 0)
    arrow.Position = UDim2.new(1, -30, 0, 0)
    arrow.BackgroundTransparency = 1
    arrow.Text = ">"
    arrow.Parent = btn

    btn.MouseEnter:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(22, 22, 38)}):Play()
        TweenService:Create(stroke, TweenInfo.new(0.2), {Color = color, Transparency = 0}):Play()
        TweenService:Create(mainTxt, TweenInfo.new(0.2), {TextColor3 = color}):Play()
        TweenService:Create(arrow, TweenInfo.new(0.2), {TextColor3 = color}):Play()
    end)
    btn.MouseLeave:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(16, 16, 26)}):Play()
        TweenService:Create(stroke, TweenInfo.new(0.2), {Color = Color3.fromRGB(32, 32, 48), Transparency = 0.4}):Play()
        TweenService:Create(mainTxt, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(245, 245, 250)}):Play()
        TweenService:Create(arrow, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(60, 60, 80)}):Play()
    end)

    btn.Parent = btnContainer
    return btn
end

local NormalBtn = createLoaderButton("⚡ VERSIÓN FULL / NORMAL", "Optimizado para ordenadores de alto rendimiento con carga completa de recursos.", Color3.fromRGB(0, 210, 255), 1)
local LightBtn = createLoaderButton("🍃 VERSIÓN ULTRA LIGHT", "Configuración de alto rendimiento diseñada para reducir el consumo y maximizar tus FPS.", Color3.fromRGB(0, 255, 140), 2)
-- =============================================================================
--    HERMANOS CP // HUB LOADER (OWNER ALWAYS FIXED & UPDATED) - PARTE 2
-- =============================================================================

-- BARRA INFORMATIVA INFERIOR
local infoBar = Instance.new("Frame")
infoBar.Size = UDim2.new(1, -36, 0, 36)
infoBar.Position = UDim2.new(0, 18, 1, -48)
infoBar.BackgroundColor3 = Color3.fromRGB(14, 14, 24)
infoBar.Parent = MainFrame

local infoBarCorner = Instance.new("UICorner")
infoBarCorner.CornerRadius = UDim.new(0, 6)
infoBarCorner.Parent = infoBar

local infoBarStroke = Instance.new("UIStroke")
infoBarStroke.Color = Color3.fromRGB(40, 40, 60)
infoBarStroke.Thickness = 1
infoBarStroke.Parent = infoBar

local infoTxt = Instance.new("TextLabel")
infoTxt.Size = UDim2.new(1, -14, 1, 0)
infoTxt.Position = UDim2.new(0, 10, 0, 0)
infoTxt.BackgroundTransparency = 1
infoTxt.Font = Enum.Font.Ubuntu
infoTxt.TextSize = 11
infoTxt.TextColor3 = Color3.fromRGB(180, 190, 220)
infoTxt.TextXAlignment = Enum.TextXAlignment.Left
infoTxt.TextWrapped = true
infoTxt.Parent = infoBar

task.spawn(function()
    while true do
        local textoLicencia = datosLicencia.fechaLimite == "Infinito" and "Licencia Inf\nTiempo de Licencia: Infinito" or ("Licencia Temp\nTiempo Restante: " .. obtenerTiempoRestanteTexto(datosLicencia.fechaLimite))
        infoTxt.Text = string.format("👤 Usuario: %s  |  📅 %s", LocalPlayer.Name, textoLicencia)
        task.wait(1)
    end
end)

-- PANEL OWNER (AMBOS OWNERS SIEMPRE ARRIBA)
if datosLicencia.rol == "OWNER" or jugadorId == 10908318368 or jugadorId == 4252198913 then
    infoBar.Size = UDim2.new(1, -120, 0, 36)
    
    local ownerBtn = Instance.new("TextButton")
    ownerBtn.Size = UDim2.new(0, 75, 0, 36)
    ownerBtn.Position = UDim2.new(1, -93, 1, -48)
    ownerBtn.BackgroundColor3 = Color3.fromRGB(30, 15, 45)
    ownerBtn.Font = Enum.Font.GothamBold
    ownerBtn.TextSize = 10
    ownerBtn.TextColor3 = Color3.fromRGB(255, 215, 0)
    ownerBtn.Text = "👑 OWNER"
    ownerBtn.Parent = MainFrame
    
    local ownerCorner = Instance.new("UICorner")
    ownerCorner.CornerRadius = UDim.new(0, 6)
    ownerCorner.Parent = ownerBtn
    
    local ownerStroke = Instance.new("UIStroke")
    ownerStroke.Color = Color3.fromRGB(255, 215, 0)
    ownerStroke.Thickness = 1
    ownerStroke.Parent = ownerBtn

    local OwnerPanel = Instance.new("Frame")
    OwnerPanel.Size = UDim2.new(1, 0, 1, -48)
    OwnerPanel.Position = UDim2.new(0, 0, 0, 48)
    OwnerPanel.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
    OwnerPanel.BorderSizePixel = 0
    OwnerPanel.Visible = false
    OwnerPanel.Parent = MainFrame
    
    local panelSubtitle = Instance.new("TextLabel")
    panelSubtitle.Font = Enum.Font.GothamBold
    panelSubtitle.TextSize = 11
    panelSubtitle.TextColor3 = Color3.fromRGB(255, 215, 0)
    panelSubtitle.Size = UDim2.new(1, -36, 0, 20)
    panelSubtitle.Position = UDim2.new(0, 18, 0, 15)
    panelSubtitle.BackgroundTransparency = 1
    panelSubtitle.TextXAlignment = Enum.TextXAlignment.Left
    panelSubtitle.Text = "👑 PANEL OWNER // GESTIÓN DE LICENCIAS"
    panelSubtitle.Parent = OwnerPanel

    local listScroll = Instance.new("ScrollingFrame")
    listScroll.Size = UDim2.new(1, -36, 1, -85)
    listScroll.Position = UDim2.new(0, 18, 0, 45)
    listScroll.BackgroundTransparency = 1
    listScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
    listScroll.ScrollBarThickness = 2
    listScroll.ScrollBarImageColor3 = Color3.fromRGB(0, 180, 255)
    listScroll.Parent = OwnerPanel
    
    local listLayout = Instance.new("UIListLayout")
    listLayout.Padding = UDim.new(0, 8)
    listLayout.SortOrder = Enum.SortOrder.LayoutOrder
    listLayout.Parent = listScroll

    -- SISTEMA DE ORDENAMIENTO ESTRICTO (OWNERS SIEMPRE PRIMERO)
    local listaOrdenada = {}
    for id, info in pairs(Licencias) do
        table.insert(listaOrdenada, {id = id, info = info})
    end

    table.sort(listaOrdenada, function(a, b)
        local aIsOwner = (a.info.rol == "OWNER") or (a.id == 10908318368) or (a.id == 4252198913)
        local bIsOwner = (b.info.rol == "OWNER") or (b.id == 10908318368) or (b.id == 4252198913)
        
        if aIsOwner and not bIsOwner then
            return true
        elseif not aIsOwner and bIsOwner then
            return false
        end
        
        local aDias = (type(a.info.diasIniciales) == "number") and a.info.diasIniciales or 0
        local bDias = (type(b.info.diasIniciales) == "number") and b.info.diasIniciales or 0
        return aDias > bDias
    end)

    -- Renderizar en base a la lista ya ordenada
    for _, entrada in ipairs(listaOrdenada) do
        local id = entrada.id
        local info = entrada.info

        local itemContainer = Instance.new("Frame")
        itemContainer.Size = UDim2.new(1, -4, 0, 36)
        itemContainer.BackgroundColor3 = Color3.fromRGB(16, 16, 26)
        itemContainer.ClipsDescendants = true
        
        local rowCorner = Instance.new("UICorner")
        rowCorner.CornerRadius = UDim.new(0, 6)
        rowCorner.Parent = itemContainer

        local rowStroke = Instance.new("UIStroke")
        rowStroke.Color = Color3.fromRGB(30, 30, 45)
        rowStroke.Thickness = 1
        rowStroke.Parent = itemContainer
        
        local txt = Instance.new("TextLabel")
        txt.Size = UDim2.new(1, -40, 0, 36)
        txt.Position = UDim2.new(0, 14, 0, 0)
        txt.BackgroundTransparency = 1
        txt.Font = Enum.Font.Ubuntu
        txt.TextSize = 12
        txt.TextColor3 = Color3.fromRGB(0, 210, 255)
        txt.TextXAlignment = Enum.TextXAlignment.Left
        txt.Text = "⏳ Buscando Servidor de Roblox..."
        txt.Parent = itemContainer

        local dropdownBtn = Instance.new("TextButton")
        dropdownBtn.Font = Enum.Font.GothamBold
        dropdownBtn.TextSize = 11
        dropdownBtn.TextColor3 = Color3.fromRGB(0, 180, 255)
        dropdownBtn.Size = UDim2.new(0, 30, 0, 36)
        dropdownBtn.Position = UDim2.new(1, -30, 0, 0)
        dropdownBtn.BackgroundTransparency = 1
        dropdownBtn.Text = "▼"
        dropdownBtn.Parent = itemContainer

        local dropdownFrame = Instance.new("Frame")
        dropdownFrame.Size = UDim2.new(1, -20, 0, 75)
        dropdownFrame.Position = UDim2.new(0, 14, 0, 36)
        dropdownFrame.BackgroundTransparency = 1
        dropdownFrame.Parent = itemContainer

        local detailsTxt = Instance.new("TextLabel")
        detailsTxt.Size = UDim2.new(1, 0, 1, 0)
        detailsTxt.BackgroundTransparency = 1
        detailsTxt.Font = Enum.Font.Ubuntu
        detailsTxt.TextSize = 11
        detailsTxt.TextColor3 = Color3.fromRGB(140, 150, 175)
        detailsTxt.TextXAlignment = Enum.TextXAlignment.Left
        detailsTxt.TextYAlignment = Enum.TextYAlignment.Top
        detailsTxt.TextWrapped = true
        detailsTxt.Parent = dropdownFrame

        itemContainer.Parent = listScroll

        local cachedName = "ID: " .. tostring(id)
        task.spawn(function()
            local success, username = pcall(function() return Players:GetNameFromUserIdAsync(id) end)
            if success and username then cachedName = username end
            
            if info.rol:find("OWNER") then
                txt.TextColor3 = Color3.fromRGB(255, 215, 0)
                txt.Text = string.format("👤 %s | 👑 %s", cachedName, info.rol)
                dropdownBtn.TextColor3 = Color3.fromRGB(255, 215, 0)
            else
                txt.Text = string.format("👤 %s | ⭐ %s", cachedName, info.rol == "PRUEBA GRATIS" and "PRUEBA GRATIS (3 DÍAS)" or string.format("USUARIO (%d DÍAS)", info.diasIniciales))
            end
        end)

        task.spawn(function()
            while true do
                local estaOnline = Players:GetPlayerByUserId(id) ~= nil and "🟢 En línea" or "🔴 Desconectado"
                local activadoTexto = typeof(info.diasIniciales) == "number" and (info.diasIniciales .. " días asignados") or info.diasIniciales
                
                detailsTxt.Text = string.format(
                    "• Nombre: %s\n• ID Cuenta: %s\n• Tiempo Activado: %s\n• Tiempo Restante: %s\n• Estado: %s",
                    cachedName, tostring(id), activadoTexto, obtenerTiempoRestanteTexto(info.fechaLimite), estaOnline
                )
                task.wait(1)
            end
        end)

        local abierto = false
        dropdownBtn.MouseButton1Click:Connect(function()
            abierto = not abierto
            dropdownBtn.Text = abierto and "▲" or "▼"
            local targetSize = abierto and UDim2.new(1, -4, 0, 120) or UDim2.new(1, -4, 0, 36)
            TweenService:Create(itemContainer, TweenInfo.new(0.2, Enum.EasingStyle.Quad), {Size = targetSize}):Play()
            task.delay(0.2, function() listScroll.CanvasSize = UDim2.new(0, 0, 0, listLayout.AbsoluteContentSize.Y) end)
        end)
    end

    local function togglePanel(viewingPanel)
        OwnerPanel.Visible = viewingPanel
        backBtn.Visible = viewingPanel
        btnContainer.Visible = not viewingPanel
        subtitle.Visible = not viewingPanel
        ownerBtn.Visible = not viewingPanel
        title.Position = viewingPanel and UDim2.new(0, 42, 0, 0) or UDim2.new(0, 18, 0, 0)
    end

    ownerBtn.MouseButton1Click:Connect(function() togglePanel(true) end)
    backBtn.MouseButton1Click:Connect(function() togglePanel(false) end)
end

-- LÓGICA DE CIERRE CON SOPORTE DE EJECUCIÓN RAW
local function closeWithEffect(callback)
    TweenService:Create(mainStroke, TweenInfo.new(0.2), {Transparency = 1}):Play()
    local closeTween = TweenService:Create(MainFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {Size = UDim2.new(0, 390, 0, 0), Position = UDim2.new(0.5, -195, 0.5, 0)})
    closeTween:Play()
    closeTween.Completed:Connect(function()
        LoaderGui:Destroy()
        callback()
    end)
end

NormalBtn.MouseButton1Click:Connect(function()
    closeWithEffect(function() 
        print("[Loader] Descargando y Ejecutando Versión Normal...")
        local normalRawLink = "https://raw.githubusercontent.com/sob76811-sketch/normal/refs/heads/main/obfuscated_script-1784562776718.lua"
        
        local success, err = pcall(function()
            loadstring(game:HttpGet(normalRawLink))()
        end)
        if not success then
            warn("Error al cargar la Versión Normal: " .. tostring(err))
        end
    end)
end)

LightBtn.MouseButton1Click:Connect(function()
    closeWithEffect(function() 
        print("[Loader] Descargando y Ejecutando Versión Light...")
        local lightRawLink = "https://raw.githubusercontent.com/sob76811-sketch/litg/refs/heads/main/README.md"
        
        local success, err = pcall(function()
            loadstring(game:HttpGet(lightRawLink))()
        end)
        if not success then
            warn("Error al cargar la Versión Ultra Light: " .. tostring(err))
        end
    end)
end)

-- ARRASTRE COMPATIBLE CON XENO Y OTROS EXECUTORS
local dragging = false
local dragStart = Vector3.zero
local startPos = UDim2.new()

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        TweenService:Create(MainFrame, TweenInfo.new(0.08, Enum.EasingStyle.Linear), {
            Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        }):Play()
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)
